
# Android Callback
Android projemizde ```Separation of Concerns``` uygulamak yani birbiriyle bağlantılı olmayan katmanları ayırmak için callback kullanabiliriz.

### Interface tanımlama

    public interface Callback<T>{
	    public void onSuccess(T type);
	    public void onFailure(String message);
    }
    
### Method yapısı

Kullanmak için parametre olarak alıyoruz. Callbacki tanımlarken işlem bittiğinde almak istediğimiz tipi ```<T>``` kısmında belirtiyoruz.

> **Eğer tek bir tip üzerinde çalışıyorsanız ```<T>``` kullanmanıza gerek yok.**

    public class UserService {
    	public void getUserByID(int ID, Callback<User> callback);
	    public void getAllUsers(Callback<List<User>> callback);
        public void getVeriTipi(Callback<VeriTipi> callback);
    }

Interfacede tanımladığımız olay gerçekleştiğinde callback.olayAdı(Tip) olarak çağırıyoruz.

    public void getUserByID(int ID, Callback<User> callback){
        User user = new User();
        // Burada işlemleri yazabiliriz, örnek olarak; Web Service'e bir request yapmak.
        if (kontrol){
            callback.onSuccess(user);
        } else {
            callback.onFailure("Hata");
        }
    }
    
### Kullanım

İşlemin sonucunu göstermek istediğimiz activityde ise şöyle kullanıyoruz.

    public class MainActivity extends AppCompatActivity {

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            
            UserService userService = new UserService();
            userService.getUserByID(ID, new Callback<User>() {
                @Override
                public void onSuccess(User user) {
                    Toast.makeText(getApplicationContext(), user.fullName, Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onFailure(String message) {
                    Toast.makeText(getApplicationContext(), message, Toast.LENGTH_SHORT).show();
                }
            });
        }

    }
