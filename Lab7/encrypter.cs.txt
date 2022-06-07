using System.IO;

namespace Encrypter
{
    class Program
    {
        static void Main(string[] args)
        {
            if (args.Length != 3)
            {
                Console.WriteLine("Usage: [path to decrypted exe file] [Start byte number] [Amount of bytes]");
                return;
            }
            byte[] exe = File.ReadAllBytes(args[0]);
            byte[] key =  new byte[] { 0x18, 0x28};
           
            int start = int.Parse(args[1]);
            int stop = int.Parse(args[2]);
            byte reminder = 0;
            for (int i = start, k = 0; k < stop; i+=2, k+=2)
            {
                reminder = (byte)((exe[i] + key[1]) >> 8);
                exe[i] = (byte)(exe[i] + key[1]);                

                exe[i + 1] = (byte)(exe[i + 1] + key[0]);
                if (reminder != 0)
                {
                    exe[i + 1]++;
                    reminder = 0;
                }
            }

            string path = args[0];
            string file = Path.GetFileName(path);
            string filename = Path.GetFileNameWithoutExtension(path);
            string NewPath = path.Replace(file, filename + "_enc.exe");
            File.WriteAllBytes(NewPath, exe);
            Console.WriteLine("File encrypted at: " + NewPath);
        }
    }
}
