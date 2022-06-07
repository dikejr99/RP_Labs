using System;

namespace Lab8CS_hack
{
    internal class Program
    {
		

		static void Main(string[] args)
        {
			uint password = 0;
			string name = "Ayoub El-Haddadi";
			foreach (char c in name)
			{
				password += (uint)('Ɯ' + 'Ĵ' * c) + 257333U + 140U;
			}
			Console.WriteLine(password);
			Console.ReadLine();
		}
    }
}
