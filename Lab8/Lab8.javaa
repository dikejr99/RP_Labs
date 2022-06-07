import javax.swing.JOptionPane;
import java.util.Scanner; 

public class Lab8{

    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        Password pwd = new Password();
        System.out.println("Input your password: ");
        int password =  scan.nextInt();
        scan.close();
        if(password == pwd.getPassword()){      //4568756
            JOptionPane.showMessageDialog(null, "Correct password", "Password check", JOptionPane.INFORMATION_MESSAGE);
        }else{
            JOptionPane.showMessageDialog(null, "Incorrect password", "Password check", JOptionPane.ERROR_MESSAGE);
        }
        //System.out.println(pwd.getPassword());
        
    }
}

class Password{
    private int password = 0; //or long
    private String name = "Ayoub El-Haddadi";
    private int b = 140;
    private int c = 412;
    private int d = 929;
    private int e = 308;
    private int f = 277;

    public Password(){
        for(char a : name.toCharArray()){
            password += c + (e * a) + (f * d) + b;
        }
    }
    
    public int getPassword(){
        return password;
    }
}