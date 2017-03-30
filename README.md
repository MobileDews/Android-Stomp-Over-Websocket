# Android-Stomp-Over-Websocket


Android Stomp over websocket can be used to connect android device with stomp over websocket.


GRADLE

Since this project contains retrolamda add the following classpath in project


            classpath 'me.tatarka:gradle-retrolambda:3.2.0'
            
Add the JitPack repository to your build file  
          
        	allprojects {
        		repositories {
        			...
        			maven { url 'https://jitpack.io' }
        		}
        	}
          	
          	
Add the following in your app build.gradle  
        	
        apply plugin: 'me.tatarka.retrolambda'
            
            android {
                   .............
                compileOptions {
                    sourceCompatibility JavaVersion.VERSION_1_8
                    targetCompatibility JavaVersion.VERSION_1_8
                }
            }
            
            
              dependencies {
                  ............................
                compile 'org.java-websocket:Java-WebSocket:1.3.0'
                compile 'com.github.MobileDews:Android-Stomp-Over-Websocket:1.0.1'
            }


Add the following code to the MainActivity.java


    public class MainActivity extends AppCompatActivity {

    Button connect;
    public static final String TAG="StompClient";
    private StompClient mStompClient;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        connect=(Button)findViewById(R.id.button2);
        connect.setOnClickListener(v -> {
            connectStomp();
        });
     }

    private void connectStomp() {
        // replace your websocket url
        mStompClient = Stomp.over(WebSocket.class, "ws://localhost:8000/StompApp/websocket");
        // replace with your topics
        mStompClient.topic("/topic/Mytopics")
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(topicMessage -> {

                    toast(""+ topicMessage.getPayload());
                });



        mStompClient.connect();
        mStompClient.lifecycle()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(lifecycleEvent -> {
                    switch (lifecycleEvent.getType()) {
                        case OPENED:
                            toast("Stomp connection opened");
                            break;
                        case ERROR:
                            toast("Stomp connection error");
                            break;
                        case CLOSED:
                            toast("Stomp connection closed");
                    }
                });
     }
    private void toast(String text) {
        Log.i(TAG, text);
        Toast.makeText(this, text, Toast.LENGTH_SHORT).show();
     }

    }