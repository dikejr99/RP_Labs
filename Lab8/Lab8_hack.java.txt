public class Lab8_hack {

    public static void main(String[] args) {
        int password = 0;  
        String name = "Ayoub El-Haddadi";        
        int b = 140;        
        int c = 412;        
        int d = 929;        
        int e = 308;        
        int f = 277;
  
        for (char a : name.toCharArray()){
            password +=c + e * a + f * d + b; 
        }
        System.out.println(password);
    }
}