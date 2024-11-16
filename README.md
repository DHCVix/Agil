using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Data.SqlClient;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace QLHS
{
    public partial class Form1 : Form
    {
        private string connectionString;

        public Form1()
        {
            InitializeComponent();
        }

        private void thoat_Click(object sender, EventArgs e)
        {
            DialogResult result = MessageBox.Show("Bạn chắc chắn muốn thoát?", "Xác nhận", MessageBoxButtons.YesNo, MessageBoxIcon.Question);
            if (result == DialogResult.Yes)
            {
                Environment.Exit(0);
            }
        }

        private void luu_Click(object sender, EventArgs e, string connectionString)
        {


            

        }

        private void luu_Click(object sender, EventArgs e)
        {
            string maHS = MaHS.Text.Trim();
            string tenHS = TenHS.Text.Trim();


            DateTime ngaySinh;
            if (!DateTime.TryParse(NgaySinh.Text, out ngaySinh))
            {
                MessageBox.Show("Ngày sinh không hợp lệ!", "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                return;
            }
            string ngaySinhStr = ngaySinh.ToString("yyyy-MM-dd");

            string diaChi = DiaChi.Text.Trim();
            float dtb;
            bool isDtbValid = float.TryParse(DTB.Text.Trim(), out dtb);
            string maLop = MaLop.Text.Trim();

            if (string.IsNullOrEmpty(maHS) || string.IsNullOrEmpty(tenHS) || !isDtbValid || string.IsNullOrEmpty(maLop))
            {
                MessageBox.Show("Vui lòng nhập đầy đủ và đúng thông tin!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                return;
            }

            string query = "INSERT INTO HOCSINH (MaHS, TenHS, NgaySinh, DiaChi, DTB, MaLop) VALUES (@MaHS, @TenHS, @NgaySinh, @DiaChi, @DTB, @MaLop)";

            try
            {
                using (SqlConnection connection = new SqlConnection(connectionString))
                {
                    connection.Open();

                    using (SqlCommand command = new SqlCommand(query, connection))
                    {
                        command.Parameters.AddWithValue("@MaHS", maHS);
                        command.Parameters.AddWithValue("@TenHS", tenHS);
                        command.Parameters.AddWithValue("@NgaySinh", NgaySinh);
                        command.Parameters.AddWithValue("@DiaChi", diaChi);
                        command.Parameters.AddWithValue("@DTB", dtb);
                        command.Parameters.AddWithValue("@MaLop", maLop);

                        int rowsAffected = command.ExecuteNonQuery();
                        if (rowsAffected > 0)
                        {
                            MessageBox.Show("Lưu thông tin học sinh thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
                        }
                        else
                        {
                            MessageBox.Show("Không thể lưu thông tin học sinh!", "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Question);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show($"Đã xảy ra lỗi: {ex.Message}", "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Question);
            }
        }
    }
}
