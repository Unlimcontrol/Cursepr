# Cursepr
 public partial class AddNewIllness : Form   // Форма додавання нової хвороби
    {
        BaseOfIllneses baseofillness = new BaseOfIllneses(); // Поле, що зберігає базу хвороб

        public AddNewIllness()
        {
            InitializeComponent();
        }

        private void AddNewIllness_Load(object sender, EventArgs e) 
        {
            Sklad sklad = ReadWriteToFile.ReadSklad();
            try
            {
                foreach (var key in sklad)
                    listBox1.Items.Add(key.name);
            }
            catch (Exception)
            { }
        }

        public bool IsValid()   
        {
            Regex regex = new Regex("[0-9_*?:%!@<>№-]");
            if (!regex.IsMatch(textBox1.Text.Trim()) && !regex.IsMatch(textBox2.Text.Trim())
                && !regex.IsMatch(textBox4.Text.Trim()) && (textBox1.Text.Trim() != String.Empty)
                && (textBox2.Text.Trim() != String.Empty) && (listBox2.SelectedIndex >= 0)
                && (textBox4.Text.Trim() != String.Empty) && (listBox1.SelectedIndex >= 0))
                    return true;
            return false;       
        }

       
        private void textBox1_Validating(object sender, CancelEventArgs e)
        {
            e.Cancel = textBox1.Text.Trim() == String.Empty;
            string textError = e.Cancel ? "Заповніть поле 'Назва'" : "";
            errorProvider1.SetError(textBox1, textError);
        }

        private void textBox2_Validating(object sender, CancelEventArgs e)
        {
            e.Cancel = textBox2.Text.Trim() == String.Empty;
            string textError = e.Cancel ? "Заповніть поле 'Симптоми'" : "";
            errorProvider1.SetError(textBox1, textError);
        }

       

        private void textBox4_Validating(object sender, CancelEventArgs e)
        {
            e.Cancel = textBox4.Text.Trim() == String.Empty;
            string textError = e.Cancel ? "Заповніть це поле 'Заходи'" : "";
            errorProvider1.SetError(textBox1, textError);
        }

        private void button1_Click(object sender, EventArgs e) 
        {
            if (!IsValid())
            {
                DialogResult = DialogResult.None;
                MessageBox.Show("Помилка у заповненні форми! Перевірте, будь ласка, правильність введених Вами даних", "", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            else
            {
               Illness ill = new Illness();
              try
                {
                    ill.Doza = listBox2.Text;                  
                }
                catch (Exception)
                {
                }

                BaseOfIllneses baseofillness = ReadWriteToFile.ReadIllness();
                ill.name = textBox1.Text;
                ill.symtomes = textBox2.Text;
                foreach(var key in listBox1.SelectedItems)
                   ill.med += "  " +key.ToString();
                ill.procedures = textBox4.Text;
                baseofillness.AddIllnessToBase(ill);
                baseofillness.Add(ill);
                ReadWriteToFile.WriteIllness(baseofillness);
                DialogResult = DialogResult.O
