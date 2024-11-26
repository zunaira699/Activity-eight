# Activity-eight
using System;
using System.Data;
using System.Data.SqlClient;
using System.Configuration;
using System.Windows.Forms;

namespace CustomerApp
{
    public partial class frmCustomerDataEntry : Form
    {
        public frmCustomerDataEntry()
        {
            InitializeComponent();
        }
        private void frmCustomerDataEntry_Load(object sender, EventArgs e)
        {
            loadCustomer();
        }
        private void loadCustomer()
        {
            string strConnection = ConfigurationManager.ConnectionStrings["DBConn"].ToString();
            using (SqlConnection objConnection = new SqlConnection(strConnection))
            {
                try
                {
                    objConnection.Open();
                    string strCommand = "SELECT * FROM CustomerTable";
                    SqlDataAdapter objAdapter = new SqlDataAdapter(strCommand, objConnection);
                    DataSet objDataSet = new DataSet();
                    objAdapter.Fill(objDataSet);
                    dtgCustomer.DataSource = objDataSet.Tables[0]; 
                }
                catch (Exception ex)
                {
                    MessageBox.Show($"Error: {ex.Message}");
                }
            }
        }
        private void dtgCustomer_CellClick(object sender, DataGridViewCellEventArgs e)
        {
            clearForm(); 
            string CustomerName = dtgCustomer.Rows[e.RowIndex].Cells[0].Value.ToString();
            displayCustomer(CustomerName); 
        }

        private void clearForm()
        {
            txtName.Text = "";
            cmbCountry.Text = "";
            radioMale.Checked = false;
            radioFemale.Checked = false;
            chkReading.Checked = false;
            chkPainting.Checked = false;
            radioMarried.Checked = false;
            radioUnmarried.Checked = false;
        }
        private void displayCustomer(string strCustomer)
        {
            string strConnection = ConfigurationManager.ConnectionStrings["DBConn"].ToString();
            using (SqlConnection objConnection = new SqlConnection(strConnection))
            {
                try
                {
                    objConnection.Open();
                    string strCommand = "SELECT * FROM CustomerTable WHERE CustomerName = @CustomerName";
                    SqlCommand objCommand = new SqlCommand(strCommand, objConnection);
                    objCommand.Parameters.AddWithValue("@CustomerName", strCustomer);

                    SqlDataAdapter objAdapter = new SqlDataAdapter(objCommand);
                    DataSet objDataSet = new DataSet();
                    objAdapter.Fill(objDataSet);
                    if (objDataSet.Tables[0].Rows.Count > 0)
                    {
                        txtName.Text = objDataSet.Tables[0].Rows[0]["CustomerName"].ToString();
                        cmbCountry.Text = objDataSet.Tables[0].Rows[0]["Country"].ToString();

                        string Gender = objDataSet.Tables[0].Rows[0]["Gender"].ToString();
                        if (Gender.Equals("Male")) radioMale.Checked = true;
                        else radioFemale.Checked = true;

                        string Hobby = objDataSet.Tables[0].Rows[0]["Hobby"].ToString();
                        if (Hobby.Equals("Reading")) chkReading.Checked = true;
                        else chkPainting.Checked = true;

                        string Married = objDataSet.Tables[0].Rows[0]["Married"].ToString();
                        if (Married.Equals("True")) radioMarried.Checked = true;
                        else radioUnmarried.Checked = true;
                    }
                    else
                    {
                        MessageBox.Show("Customer not found.");
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show($"Error: {ex.Message}");
                }
            }
        }
        private void btnUpdate_Click(object sender, EventArgs e)
        {
            string gender = radioMale.Checked ? "Male" : "Female";
            string hobby = chkReading.Checked ? "Reading" : "Painting";
            string status = radioMarried.Checked ? "True" : "False";

            string strConnection = ConfigurationManager.ConnectionStrings["DBConn"].ToString();
            using (SqlConnection objConnection = new SqlConnection(strConnection))
            {
                try
                {
                    objConnection.Open();
                    string strCommand = "UPDATE CustomerTable SET CustomerName = @Name, Country = @Country, Gender = @Gender, Hobby = @Hobby, Married = @Married WHERE CustomerName = @OriginalName";

                    using (SqlCommand objCommand = new SqlCommand(strCommand, objConnection))
                    {
                        objCommand.Parameters.AddWithValue("@Name", txtName.Text);
                        objCommand.Parameters.AddWithValue("@Country", cmbCountry.Text);
                        objCommand.Parameters.AddWithValue("@Gender", gender);
                        objCommand.Parameters.AddWithValue("@Hobby", hobby);
                        objCommand.Parameters.AddWithValue("@Married", status);
                        objCommand.Parameters.AddWithValue("@OriginalName", txtName.Tag.ToString()); 
                        objCommand.ExecuteNonQuery();  command
                    }
                    loadCustomer();
                }
                catch (Exception ex)
                {
                    MessageBox.Show($"Error: {ex.Message}");
                }
            }
        }
        private void btnDelete_Click(object sender, EventArgs e)
        {
            string customerName = txtName.Text;
            string strConnection = ConfigurationManager.ConnectionStrings["DBConn"].ToString();

            using (SqlConnection objConnection = new SqlConnection(strConnection))
            {
                try
                {
                    objConnection.Open();
                    string strCommand = "DELETE FROM CustomerTable WHERE CustomerName = @CustomerName";

                    using (SqlCommand objCommand = new SqlCommand(strCommand, objConnection))
                    {
                        objCommand.Parameters.AddWithValue("@CustomerName", customerName);
                        objCommand.ExecuteNonQuery();                     }
                    loadCustomer();
                }
                catch (Exception ex)
                {
                    MessageBox.Show($"Error: {ex.Message}");
                }
            }
        }
    }
}
