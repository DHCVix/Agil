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
        // DELE DỮ LIỆU

        // Chuỗi kết nối tới SQL Server
    string connectionString = "Data Source=YOUR_SERVER_NAME;Initial Catalog=QLHS;Integrated Security=True";

    // Lấy dữ liệu từ form
    string maHS = txtMaHS.Text.Trim();

    // Kiểm tra dữ liệu hợp lệ
    if (string.IsNullOrEmpty(maHS))
    {
        MessageBox.Show("Vui lòng nhập mã học sinh để xóa!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Warning);
        return;
    }

    // Câu lệnh SQL để xóa dữ liệu
    string query = "DELETE FROM HOCSINH WHERE MaHS = @MaHS";

    try
    {
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();

            using (SqlCommand command = new SqlCommand(query, connection))
            {
                // Thêm tham số vào câu lệnh SQL
                command.Parameters.AddWithValue("@MaHS", maHS);

                // Hiển thị xác nhận trước khi xóa
                DialogResult result = MessageBox.Show($"Bạn có chắc chắn muốn xóa học sinh với mã '{maHS}' không?", 
                                                      "Xác nhận xóa", MessageBoxButtons.YesNo, MessageBoxIcon.Question);

                if (result == DialogResult.Yes)
                {
                    // Thực thi câu lệnh
                    int rowsAffected = command.ExecuteNonQuery();
                    if (rowsAffected > 0)
                    {
                        MessageBox.Show("Xóa học sinh thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
                    }
                    else
                    {
                        MessageBox.Show("Không tìm thấy học sinh để xóa!", "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
                    }
                }
            }
        }
    }
    catch (SqlException sqlEx)
    {
        MessageBox.Show($"Lỗi cơ sở dữ liệu: {sqlEx.Message}", "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    catch (Exception ex)
    {
        MessageBox.Show($"Đã xảy ra lỗi không xác định: {ex.Message}", "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
    }
}
