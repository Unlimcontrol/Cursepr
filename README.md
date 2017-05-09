# Cursepr

    public partial class AddNewIllness : Form   // Форма додавання нової хвороби
    {
        BaseOfIllneses baseofillness = new BaseOfIllneses(); // Поле, що зберігає базу хвороб

        public AddNewIllness()
        {
            InitializeComponent();
        }

        private void AddNewIllness_Load(object sender, EventArgs e) // Завантаження форми
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

        public bool IsValid()   // Перевірка заповнення форми
        {
            Regex regex = new Regex("[0-9_*?:%!@<>№-]");
            if (!regex.IsMatch(textBox1.Text.Trim()) && !regex.IsMatch(textBox2.Text.Trim())
                && !regex.IsMatch(textBox4.Text.Trim()) && (textBox1.Text.Trim() != String.Empty)
                && (textBox2.Text.Trim() != String.Empty) && (listBox2.SelectedIndex >= 0)
                && (textBox4.Text.Trim() != String.Empty) && (listBox1.SelectedIndex >= 0))
                    return true;
            return false;       
        }

        // Перевіка вводу полів
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

        private void button1_Click(object sender, EventArgs e) // Натискання кнопки "Додати"
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
                DialogResult = DialogResult.OK;
            }

        }      
    }
 public partial class AddNewMedicine : Form  // Форма додавання нових ліків
    {
        Sklad sklad = new Sklad(); // Поле, яке зберігає "Сховище" ліків

        public AddNewMedicine()
        {
            InitializeComponent();
        }

        private void AddNewMedicine_Load(object sender, EventArgs e) // Завантаження форми
        {
            sklad = ReadWriteToFile.ReadSklad();
            try
            {
                foreach (var key in sklad)
                    listBox1.Items.Add(key.name);
            }
            catch (Exception ex)
            {
                errorProvider1.SetError(this, ex.Message);
            }
        }

        // Перевірка вводу полів
        private void textBox1_Validating(object sender, CancelEventArgs e)
        {
            e.Cancel = textBox1.Text.Trim() == String.Empty;
            string textError = e.Cancel ? "Заповніть поле 'Назва'" : "";
            errorProvider1.SetError(textBox1, textError);
        }

        private void textBox2_Validating(object sender, CancelEventArgs e)
        {
            e.Cancel = textBox2.Text.Trim() == String.Empty;
            string textError = e.Cancel ? "Заповніть поле 'Кількість'" : "";
            errorProvider1.SetError(textBox1, textError);
        }

        private void button1_Click(object sender, EventArgs e) //При натисканні на кнопку "Додати"
        {
            Medicine med = new Medicine();
            try
            {
                med.count = Convert.ToInt32(textBox2.Text);
            }
            catch(Exception)
            {   med.count = 0;  
            }
            med.name = textBox1.Text;
            foreach (var n in listBox1.SelectedItems)
                med.analogs.Add(n.ToString());
            sklad.Add(med);
            ReadWriteToFile.WriteMed(sklad);                       
        }                                                                     
    }
     [Serializable]
    public class BaseOfIllneses : List<Illness>  // Клас "База хвороб"
    {
        public List<Illness> illneses;

        public BaseOfIllneses()    // Конструктор без параметров
        {
            illneses = new List<Illness>();
        }

        public void AddIllnessToBase(Illness illness)  // Метод додавання хвороби
        {
            if (illneses.Contains(illness))
                return;
            else
                illneses.Add(illness);
        }

        public void DeleteIllness(Illness illness)     // Метод видалення хвороби
        {
            illneses.Remove(illness);
        }
    }
                            // Клас "Хвороба"
    [Serializable]
    public class Illness
    {                      
        public string name;         // Поле "Назва"
        public string symtomes;     // Поле "Симптоми"
        public string procedures;   // Поле "Заходи"
        public string med;          // Поле "Ліки"
        public string doz;          // Поле "Дозування"

                           // Відкриті властивості
        public string Name
        {
            set
            {
                name = value;
            }
            get
            {
                return name;
            }
        }
        public string Symtomes
        {
            set
            {
                symtomes =value;
            }
            get
            {
                return symtomes.ToString();
            }
        }
        public string Procedure
        {
            set
            {
                procedures =value;
            }
            get
            {
                return procedures.ToString();
            }
        }
        public string Medicine
        {
            set
            {
                med =value;
            }
            get
            {
                return med;
            }
        }
                           // Властивість "Дозування" (якщо введено некоректні дані, то присвоюється значення за замовчуванням)
        public string Doza
        {
            set
            {
                string d = "";
                string n = value;
             Regex regex = new Regex("[1-9]+$");
                if(regex.IsMatch(n))
                {
                    d += n;
                
                }
                else
                {
                    d += "50";
                }
                doz = d;
           }
            get
            {
                return doz;
            }
        }   

                              
        public Illness()   // Конструктор без пaреметрів
        {
            name = "";
            symtomes = "";
            procedures = "";
            med = "";
            doz = "";
        }

                             // Конструктор з параметром ("Назва")
        public Illness(string n)
        {           
            name = n;
            symtomes = "";
            procedures = "";
            med = "";
            doz = "";
        }

        
    }
    public partial class Information : Form     // Форма "Довідкова інформація"
    {
        public Information()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            DialogResult = DialogResult.OK;     
        }

    }
     public partial class MainForm : Form     // Головна форма  
    {
        // Поля
        BaseOfIllneses baseofillness = new BaseOfIllneses();    // База хвороб
        Sklad sklad = new Sklad();                              // База ліків
        public bool change = false;
        public MainForm()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)    // При завантаженні форми завантажуються база хвороб і база ліків
        {
            try
            {
                baseofillness = ReadWriteToFile.ReadIllness();
                sklad = ReadWriteToFile.ReadSklad();                
               
                                                               // Прив'язування даних
                bindingSource1.DataSource = baseofillness;
                medicineBindingSource.DataSource = sklad;
                
            }
            catch (Exception) { }
        }
      

        private void button1_Click(object sender, EventArgs e)         // Подія при натисканні кнопки "Пошук (хвороби)"
        {
            try
            {
                if (textBox1.Text != "") {
                    bool found = false;                                    
                    for (int i = 0; i < dataGridView1.RowCount; i++)
                    {
                        dataGridView1.Rows[i].Selected = false;
                        if (dataGridView1.Rows[i].Cells[0].Value != null)
                            if (dataGridView1.Rows[i].Cells[0].Value.ToString().ToUpper().Contains(textBox1.Text.ToUpper()))
                            {
                                dataGridView1.Rows[i].Selected = true;
                                found = true;
                            }
                    }
                    if (found == false)
                    {
                        DialogResult = DialogResult.None;
                        MessageBox.Show("Такої хвороби не знайдено!!!", "", MessageBoxButtons.OK, MessageBoxIcon.Error);
                    }
                }
               
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        private void button2_Click(object sender, EventArgs e) // Подія при натисканні кнопки "Пошук (ліки)"
        {
            try
            {                                                // Поиск в першому стовпці (dataGridView)
                if (textBox3.Text != "")
                {
                    bool found = false;
                    for (int i = 0; i < dataGridView2.RowCount; i++)
                    {
                        dataGridView2.Rows[i].Selected = false;
                        if (dataGridView2.Rows[i].Cells[0].Value != null)
                            if (dataGridView2.Rows[i].Cells[0].Value.ToString().ToUpper().Contains(textBox3.Text.ToUpper()))
                            {
                                dataGridView2.Rows[i].Selected = true;
                                found = true;
                            }
                    }
                    if (found == false)
                    {
                        DialogResult = DialogResult.None;
                        MessageBox.Show("Таких ліків не знайдено!!!", "", MessageBoxButtons.OK, MessageBoxIcon.Error);

                    }
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }


        private void button3_Click(object sender, EventArgs e) // Подія при натисканні кнопки "Пошук (симптоми)"
        {
            try
            {
                if (textBox2.Text != "")
                {
                    bool found = false;
                    for (int i = 0; i < dataGridView1.RowCount; i++)    //Поиск в другому стовпці (dataGridView)
                    {
                        dataGridView1.Rows[i].Selected = false;
                        if (dataGridView1.Rows[i].Cells[1].Value != null)
                            if (dataGridView1.Rows[i].Cells[1].Value.ToString().ToUpper().Contains(textBox2.Text.ToUpper()))
                            {
                                dataGridView1.Rows[i].Selected = true;
                                found = true;
                            }
                    }
                    if (found == false)
                    {
                        DialogResult = DialogResult.None;
                        MessageBox.Show("Такого симптому не знайдено!!!", "", MessageBoxButtons.OK, MessageBoxIcon.Error);

                    }
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }        
                                                                     // Натиснення на "Додати (нові ліки)"
        private void ДодатиНовіЛікиToolStripMenuItem_Click(object sender, EventArgs e)  
        {
            try
            {
                AddNewMedicine addmed = new AddNewMedicine();
                if (addmed.ShowDialog() == DialogResult.OK)
                {
                    Form1_Load(sender, e);
                    change = true;
    }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }
                                                                           // Натиснення на "Додати (нове захворювання)"              
        private void додатиНовеЗахворюванняToolStripMenuItem_Click(object sender, EventArgs e)
        {
            try
            {
                AddNewIllness addill = new AddNewIllness();
                if (addill.ShowDialog() == DialogResult.OK)
                {
                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }
                                                                          // Натиснення на "Видалити (існуюче захворювання)"
        private void видалитиІснуючеЗахворюванняToolStripMenuItem_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView1.SelectedRows != null)
                {
                    baseofillness.RemoveAt(dataGridView1.CurrentCellAddress.Y);
                    ReadWriteToFile.WriteIllness(baseofillness);
                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }
                                                          // При натисненні "Видалити зі складу"
        private void ВидалититиЗіСкладуToolStripMenuItem_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {

                    sklad.RemoveAt(dataGridView2.CurrentCellAddress.Y);
                    ReadWriteToFile.WriteMed(sklad);
                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        } 
                                                      // Обробка неправильних даних (dataGridView1)
        private void dataGridView1_DataError(object sender, DataGridViewDataErrorEventArgs e)
        {
            errorProvider1.SetError(dataGridView1, "Помилкові дані!");
            Form1_Load(sender,e);
        }
                                                     // Обробка неправильних даних (dataGridView2)
        private void dataGridView2_DataError(object sender, DataGridViewDataErrorEventArgs e)
        {
            errorProvider1.SetError(dataGridView2, "Помилкові дані!");
            Form1_Load(sender, e);
        }
                                                     // При натисненні "Сформувати рецепт"
        private void ЛікарськийРецептToolStripMenuItem_Click(object sender, EventArgs e)
        {
            MedicalPrescription recip = new MedicalPrescription();
            if( recip.ShowDialog()==DialogResult.OK)
            {
                Form1_Load(sender, e);
            }
        }
                                                    // При натисненні "Інформаційна довідка"
        private void ІнформаційнаДовідкаToolStripMenuItem_Click(object sender, EventArgs e)
        {
            Information ab = new Information();
            if (ab.ShowDialog() == DialogResult.OK) { }

        }
                                                    // При натисненні "Зберегти"
        private void ЗберегтиВсеToolStripMenuItem_Click(object sender, EventArgs e)
        {
            try
            {
                ReadWriteToFile.WriteIllness(baseofillness);
                ReadWriteToFile.WriteMed(sklad);
                Form1_Load(sender, e);
                change = false;
            }
            catch (Exception) { }
        }
        

        private void Form1_FormClosing(object sender, FormClosingEventArgs e) // Обробка закривання форми
        {
            if (change == true)
            {
                DialogResult d = MessageBox.Show(" Закрити програму?", "Вихід з програми (зміни збережено)", MessageBoxButtons.OKCancel);
                if (d == DialogResult.OK)
                    ЗберегтиВсеToolStripMenuItem_Click(sender, e);
               else 
                    e.Cancel = true;
               
                    
            }
            else
            {
                DialogResult d = MessageBox.Show("Закрити програму?", "Вихід з програми", MessageBoxButtons.OKCancel);
                if (d == DialogResult.OK)
                {
                    e.Cancel = false;
                }
                else
                    e.Cancel = true;
            }

        }

        // Додавання одиницю препарату
        private void ДодатиОдиницюToolStripMenuItem_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad[curr].count++;
                    ReadWriteToFile.WriteMed(sklad);
                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }        

        // Додавання партії
        private void ДесятьОдиницьToolStripMenuItem_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad.Change(curr, sklad, 10);
                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        private void одиницьToolStripMenuItem1_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad.Change(curr, sklad, 20);

                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        private void одиницьToolStripMenuItem2_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad.Change(curr, sklad, 30);

                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        // Видалення одиниці препарату
        private void видалитиОдиницюToolStripMenuItem1_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad.Change(curr, sklad, -1);

                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        // Видалення партії
        private void одиницьToolStripMenuItem3_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad.Change(curr, sklad, -10);

                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        private void одиницьToolStripMenuItem4_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    sklad.Change(curr, sklad, -20);

                    Form1_Load(sender, e);
                    change = true;
                }
            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        private void одиницьToolStripMenuItem5_Click(object sender, EventArgs e)
        {
            try
            {
                if (dataGridView2.SelectedRows != null)
                {
                    int curr = dataGridView2.CurrentCellAddress.Y;
                    if (sklad.Change(curr, sklad, -30))
                    {

                        Form1_Load(sender, e);
                        change = true;
                    }
                    else
                    {
                        DialogResult = DialogResult.None;
                        MessageBox.Show("Забагато!!!", "", MessageBoxButtons.OK, MessageBoxIcon.Error);
                    }
                }            

            }
            catch (Exception) { Form1_Load(sender, e); }
        }

        
    }
                                        // Форма для призначення медичного рецепту
    public partial class MedicalPrescription : Form
    {
        BaseOfIllneses baza; // Поле, що зберігає базу хвороб

        public MedicalPrescription()
        {
            InitializeComponent();
        }

        public bool ValidText() // Перевірка правильності введеного тексту в поля ПІБ
        {
            Regex regex = new Regex("[0-9_*?:%!@<>№-]");

            if (!regex.IsMatch(textBox1.Text.Trim()) && !regex.IsMatch(textBox2.Text.Trim())
                && !regex.IsMatch(textBox3.Text.Trim()) && !regex.IsMatch(textBox4.Text.Trim()))
                return true;
            return false; 
        }

        public bool IsValid() // Перевірка правильності заповнення всіх полів форми
        {

            if ((textBox1.Text.Trim() != String.Empty) && (textBox2.Text.Trim() != String.Empty) && (textBox3.Text.Trim() != String.Empty) && (textBox4.Text.Trim() != String.Empty))
                if ((listBox1.SelectedIndex >= 0) && (listBox3.SelectedIndex >= 0) && (listBox4.SelectedIndex >= 0))
                    return true;
            return false;
        }
 
        // При натисненні на кнопку "Сформувати рецепт"

        private void button1_Click(object sender, EventArgs e)
        {
            if (!IsValid())
            {
                DialogResult = DialogResult.None;
                MessageBox.Show("Кожне поле повинне бути заповненим!!!", "", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            else if (!ValidText())
            {
                DialogResult = DialogResult.None;
                MessageBox.Show("Перевірте правильність написання ПІБ пацієнта та відповідального лікаря!!!", "", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            else
            {
                SaveFileDialog sav = new SaveFileDialog();
                sav.ShowDialog();
                                                              // Відкриття діалогового вікна
                StreamWriter file = new StreamWriter(sav.FileName + ".txt");
                                                              // Записування у файл (текстовий)
                file.WriteLine("Прізвище Ім'я Побатькові : " + textBox1.Text + " " + textBox2.Text + " " + textBox3.Text);
                file.WriteLine("Рік народження : " + listBox1.SelectedItem);
                file.WriteLine("Діагноз : " + listBox3.SelectedItem);
                file.WriteLine("Ліки : " + baza[listBox3.SelectedIndex].Medicine + " , доза :  " + baza[listBox3.SelectedIndex].Doza);
                if (listBox2.SelectedIndex >= 0)
                      file.WriteLine("Додаткова терапія : " + listBox2.SelectedItem);
                file.WriteLine("Відповідальний лікар : " + textBox4.Text);
                file.Close(); 
                DialogResult = DialogResult.OK;
            }
        }

        private void Recipe_Load(object sender, EventArgs e)      // Завантаження форми
        {
            baza = ReadWriteToFile.ReadIllness();
            Sklad sklad = ReadWriteToFile.ReadSklad();
            try
            {
                foreach (var key in sklad)
                    listBox2.Items.Add(key.name);
            }
            catch (Exception)
            { }
            BaseOfIllneses basa = ReadWriteToFile.ReadIllness();
            try
            {
                foreach (var key in basa)
                    listBox3.Items.Add(key.name);
            }
            catch (Exception)
            { }
        }

    }
                     // Клас "Ліки"
    [Serializable]
    public class Medicine
    {              
        public string name;    // Поле "Назва"
        public int count;      // Поле "Кількість"
        public List<string> analogs;   // Поле "Аналоги"

        public string Med       // Властивість "Ім'я"
        {
            get
            {
                return name;
            }
            set
            {
                if (value != string.Empty)
                   name = value;
            }
        }
        public int Count        // Властивість "Кількість"
        {
            get
            {
                return count;
            }
            set
            {
                if (value>=0)
                    count = value;
            }
        }
        public Medicine()       // Конструктор без параметрів
        {
            name = "";
            analogs = new List<string>();
        }
        public Medicine(string sy)  // Конструктор з параметном назви ліків
        {
            name = sy;
            analogs = new List<string>();
        }

    }
                            // Клас, за доромогою якого дані завантажуються з файлів та зберігаються в файли
    class ReadWriteToFile
    {                                    
        static public void WriteIllness(BaseOfIllneses il)             // Метод для збереження бази хвороб
        {           
                FileStream f = new FileStream("BaseOfIllness.bin", FileMode.Create);
                BinaryFormatter bf = new BinaryFormatter();
                bf.Serialize(f, il);
                f.Dispose();                   
        }

        static public void WriteMed(Sklad skl)          // Метод для збереження бази ліків
        {
            FileStream f = new FileStream("Stock.bin", FileMode.Create);
            BinaryFormatter bf = new BinaryFormatter();
            bf.Serialize(f, skl);
            f.Dispose();
        }

        static public BaseOfIllneses ReadIllness()       // Метод для завантаження бази хвороб
        {
            FileStream f = new FileStream("BaseOfIllness.bin", FileMode.Open);
            BinaryFormatter bf = new BinaryFormatter();
            BaseOfIllneses bl = (BaseOfIllneses)bf.Deserialize(f);
            f.Dispose();
            return bl;  
        }

        static public Sklad ReadSklad()              // Метод для завантаження бази ліків
        {
            FileStream f = new FileStream("Stock.bin", FileMode.Open);
            BinaryFormatter bf = new BinaryFormatter();
            Sklad sl = (Sklad)bf.Deserialize(f);
            f.Dispose();
            return sl;  
        }
    }
    
                                        // Клас "База ліків" 
    [Serializable]
    public class Sklad : List<Medicine>
    {
     public List<Medicine> onsklad;    // Поле, що зберігає перелік ліків

        public Sklad()                // Конструктор без параметрів
        {
            onsklad = new List<Medicine>();
        }
                                   
        public void AddToSklad(Medicine m, int amoun)   // Додавання ліків на базу
        {
            if (onsklad.Contains(m))
            {
                onsklad[onsklad.IndexOf(m)].count += amoun;
            }
            else
            {
                onsklad.Add(m);
                onsklad[onsklad.IndexOf(m)].count += amoun;
            }
        }
                                     // Проверка наявності певних ліків
        public bool IsOnSklad(Medicine m)
        {
            if (onsklad.Contains(m) && (onsklad[onsklad.IndexOf(m)].count > 0))
                return true;
            else
                return false;
        }      

        public bool Change(int m, Sklad sklad, int g) // Зміна кількості ліків на базі
        {

            if (g > 0 || (g < 0 && sklad[m].count >= (-g)))
            {
                sklad[m].count += g;
                ReadWriteToFile.WriteMed(sklad);
                return true;
            }
            return false;
           
        }
    }
    static class Program
    {
        /// <summary>
        /// Главная точка входа для приложения.
        /// </summary>
        [STAThread]
        static void Main()
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            Application.Run(new MainForm());
        }
    }
