# BluetoothDemoOne
One Button enable/disable bluetooth
單鍵開啟/關閉藍牙: 若藍牙為開啟，點下按鈕，就會關閉手機藍牙。若藍牙為關閉，則會跳出系統預設的詢問使窗"是否允許APP開啟藍牙"，選"是"則會開啟手機藍牙

參考學習 : https://youtu.be/QVD_8PrvG4Q

//Manifest加入兩項權限

    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>


//MainAct(全部照貼)

package com.example.bluetoothonandoff; //記得換成自己的packagename


public class MainActivity extends AppCompatActivity {
    private static final String TAG ="MainActivity";
    BluetoothAdapter btAdapter;
    Button btn1;


    // Create a BroadcastReceiver for ACTION_FOUND.
    private final BroadcastReceiver mBroadcastReceiver1 = new BroadcastReceiver() {
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            if (action.equals(BluetoothAdapter.ACTION_STATE_CHANGED)){
                final int state = intent.getIntExtra(BluetoothAdapter.EXTRA_STATE, BluetoothAdapter.ERROR);
                switch (state){
                    case BluetoothAdapter.STATE_OFF:
                        Log.d(TAG,"onReceive: STATE OFF");
                        break;
                    case BluetoothAdapter.STATE_TURNING_OFF:
                        Log.d(TAG, "BroadcrastReceiver1 : state turning OFF");
                        break;
                    case  BluetoothAdapter.STATE_ON:
                        Log.d(TAG,"mBroadcastReceiver1:state_ON");
                        break;
                    case BluetoothAdapter.STATE_TURNING_ON:
                        Log.d(TAG, "mBroadcastReceiver1:State turning ON");
                        break;
                }
            }
        }
    };


    @Override
    protected void onDestroy() {
        Log.d(TAG,"onDestroy called");
        super.onDestroy();
        unregisterReceiver(mBroadcastReceiver1);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btAdapter = BluetoothAdapter.getDefaultAdapter();


        btn1 = findViewById(R.id.btn1);
        btn1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.d(TAG,"button enable/disable");
                enableDisableBT();
            }
        });

    }

    private void enableDisableBT() {
        if(btAdapter == null)
        {

            Log.d(TAG, "缺少藍牙裝置 ");
        }
        if(!btAdapter.isEnabled()){
            Log.d(TAG,"bt enale");
            Intent enableIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);
            startActivity(enableIntent);
            IntentFilter btIntent =new IntentFilter(BluetoothAdapter.ACTION_STATE_CHANGED);
            registerReceiver(mBroadcastReceiver1, btIntent);
        }
        if(btAdapter.isEnabled()){
            Log.d(TAG,"BT disable");
            btAdapter.disable();
            IntentFilter btIntent =new IntentFilter(BluetoothAdapter.ACTION_STATE_CHANGED);
            registerReceiver(mBroadcastReceiver1, btIntent);
        }
    }
}

//Layout 加一個按鈕(底部是 ConstraintLayout)

    <Button
        android:id="@+id/btn1"
        android:text="Turn ON/OFF Bluetooth"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:layout_marginBottom="8dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.703"
        tools:layout_editor_absoluteX="0dp" />
