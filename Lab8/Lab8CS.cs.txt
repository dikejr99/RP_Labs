using System;
using System.Windows.Forms;

namespace Lab8CS
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Input your password: ");
            uint inputPassword = uint.Parse(Console.ReadLine());
            Password pwd = new Password();
            if(inputPassword == pwd.getPassword())          //4568756
            {
                MessageBox.Show("Correct password", "Password check",MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            else
            {
                MessageBox.Show("Incorrect password", "Password check", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }
    }

    class Password
    {
        private uint password = 0;
        private string name = "Ayoub El-Haddadi";
        const int b = 140;
        const int c = 412;
        const int d = 929;
        const int e = 308;
        const int f = 277;
        public Password()
        {
            foreach(char a in name)
            {
               password += (uint)(c + (e * a) + (f * d) + b);
            }       
        }

        public uint getPassword()
        {
            return password;
        }
    }
}
