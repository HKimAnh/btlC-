# btlC-
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
using System.Data.Sql; 


namespace BTL
{
    public partial class banhang : Form
    {
        string chuoiketnoi = "Data Source=2HNGKMNH1\\SQLEXPRESS; Initial Catalog = QLMS; Integrated Security=True";
        SqlConnection conn = null;
        DataTable dtBH = new DataTable();
        DataTable dtTK = new DataTable();
        SqlDataAdapter daBH, daTK;
        string maSach = "";

        public banhang()
        {
            InitializeComponent();
        }


        private void banhang_Load(object sender, EventArgs e)
        {
            SqlConnection conn = new SqlConnection(chuoiketnoi);
            conn.Open();
            string sql = "Select * From SACH";
            daBH = new SqlDataAdapter(sql, conn);
            daBH.Fill(dtBH);
            dtgvKH.DataSource = dtBH;

            string sqlKhoSach = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From SACH";
            daTK = new SqlDataAdapter(sqlKhoSach, conn);
            daTK.Fill(dtTK);
            dtgvTK.DataSource = dtTK;
            conn.Close();
        }

        private void banhang_FormClosed(object sender, FormClosedEventArgs e)
        {
            Application.Exit();
        }

        private void OpenConnectSql()
        {
            conn = new SqlConnection(chuoiketnoi);
            conn.Open();
        }

        private void CloseConnectSql()
        {
            conn.Close();
        }

        private void button3_Click(object sender, EventArgs e) //Tmkiem 
        {
            OpenConnectSql();
            dtTK.Clear();
            string sqlKhoSach = "";
            if (tbTS.Text != "")
            {
                sqlKhoSach = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From SACH WHERE TenSach = '" + tbTS.Text + "'";
                daTK = new SqlDataAdapter(sqlKhoSach, conn);
                daTK.Fill(dtTK);
            }
            else if (tbTG.Text != "")
            {
                sqlKhoSach = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From SACH WHERE TacGia = '" + tbTG.Text + "'";
                daTK = new SqlDataAdapter(sqlKhoSach, conn);
                daTK.Fill(dtTK);
            }
            else if (tbNXB.Text != "")
            {
                sqlKhoSach = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From SACH WHERE NhaXuatBan = '" + tbNXB.Text + "'";
                daTK = new SqlDataAdapter(sqlKhoSach, conn);
                daTK.Fill(dtTK);
            }
            else
            {
                sqlKhoSach = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From SACH";
                daTK = new SqlDataAdapter(sqlKhoSach, conn);
                daTK.Fill(dtTK);
            }

            dtgvTK.DataSource = dtTK;
            CloseConnectSql();
        }

        private void button4_Click(object sender, EventArgs e)
        {
            DataGridViewRow row = (DataGridViewRow)dtgvTK.Rows[dtgvTK.CurrentCell.RowIndex].Clone();
            row.Cells[0].Value = dtgvTK.Rows[dtgvTK.CurrentCell.RowIndex].Cells[0].Value;
            row.Cells[1].Value = dtgvTK.Rows[dtgvTK.CurrentCell.RowIndex].Cells[1].Value;
            row.Cells[2].Value = dtgvTK.Rows[dtgvTK.CurrentCell.RowIndex].Cells[2].Value;
            row.Cells[3].Value = dtgvTK.Rows[dtgvTK.CurrentCell.RowIndex].Cells[3].Value;
            row.Cells[4].Value = dtgvTK.Rows[dtgvTK.CurrentCell.RowIndex].Cells[4].Value;
            dtgvTT.Rows.Add(row);
        }

        private void button5_Click(object sender, EventArgs e)
        {
            int selectedIndex = dtgvTT.CurrentCell.RowIndex;
            if (selectedIndex > -1)
            {
                dtgvTT.Rows.RemoveAt(selectedIndex);
                dtgvTT.Refresh();
            }
        }

        private void bt1_Click(object sender, EventArgs e) //Them
        {
            OpenConnectSql();
            if (tbMS.Text != "")
            {
                string sql = "insert into SACH values('" + tbMS.Text + "', N'" 
                    + tbTen.Text + "', N'" + tbTL.Text + "', '" + 0 + "','" 
                    + tbGT.Text + "',N'" + TG.Text + "',N'" + NXB.Text + "');";
                SqlCommand cmd = new SqlCommand(sql, conn);
                cmd.ExecuteNonQuery();
                dtBH.Rows.Clear();
                daBH.Fill(dtBH);
            }
            CloseConnectSql();  
        }

        private void btTT_Click(object sender, EventArgs e) //Thanh toan
        {
            int Tongtien = 0;
            for (int i = 0; i < dtgvTT.Rows.Count; ++i)
            {
                Tongtien += Convert.ToInt32(dtgvTT.Rows[i].Cells[1].Value);
            }
            tbTT.Text = Tongtien.ToString();
            tbSL.Text = (dtgvTT.Rows.Count - 1).ToString();
        }

        private void button1_Click(object sender, EventArgs e) //Sua
        {
            OpenConnectSql();
            string sql = "UPDATE SACH SET MaSach = '" + tbMS.Text + "', TenSach = N'" 
                + tbTen.Text + "', TheLoai = N'" + tbTL.Text + "', SoLuong = '" + (int)10 + "', Giatien = '"
                + Convert.ToInt32(tbGT.Text) + "', TacGia = N'" + TG.Text + "', NhaXuatBan = N'" + NXB.Text 
                + "' WHERE MaSach = '" + tbMS.Text + "';";
            SqlCommand cmd = new SqlCommand(sql, conn);
            cmd.ExecuteNonQuery();
            dtBH.Rows.Clear();
            tbMS.Text = "";
            tbTen.Text = "";
            tbTL.Text = "";
            tbGT.Text = "";
            NXB.Text = "";
            TG.Text = "";
            daBH.Fill(dtBH);
            CloseConnectSql();
        }

        
        private void button2_Click(object sender, EventArgs e) //Xoa
        {
            OpenConnectSql();
            string sql = "DELETE FROM SACH WHERE MaSach = '" + tbMS.Text + "'";
            SqlCommand cmd = new SqlCommand(sql, conn);
            cmd.ExecuteNonQuery();
            dtBH.Rows.Clear();
            tbMS.Text = "";
            tbTen.Text = "";
            tbTL.Text = "";
            tbGT.Text = "";
            NXB.Text = "";
            TG.Text = "";
            daBH.Fill(dtBH);
            CloseConnectSql();
        }

        private void dtgvKH_CellClick(object sender, DataGridViewCellEventArgs e)
        {
            foreach (DataGridViewCell oneCell in dtgvKH.SelectedCells)
            {
                tbMS.Text = oneCell.DataGridView.Rows[dtgvKH.CurrentCell.RowIndex].Cells[0].Value.ToString();
                tbTen.Text = oneCell.DataGridView.Rows[dtgvKH.CurrentCell.RowIndex].Cells[1].Value.ToString();
                tbTL.Text = oneCell.DataGridView.Rows[dtgvKH.CurrentCell.RowIndex].Cells[2].Value.ToString();
                tbGT.Text = oneCell.DataGridView.Rows[dtgvKH.CurrentCell.RowIndex].Cells[4].Value.ToString();
                NXB.Text = oneCell.DataGridView.Rows[dtgvKH.CurrentCell.RowIndex].Cells[6].Value.ToString();
                TG.Text = oneCell.DataGridView.Rows[dtgvKH.CurrentCell.RowIndex].Cells[5].Value.ToString();
            }
        }

       

        private void button6_Click(object sender, EventArgs e) //Thong ke
        { 
            SqlConnection conn = new SqlConnection(chuoiketnoi);
            conn.Open();
            dtTK.Clear();
            string sqlThongke = "";
            if (tbTS.Text != "")
            {
                sqlThongke = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From HOADON WHERE TenSach = '" + tbTS.Text + "'";
                daTK = new SqlDataAdapter(sqlThongke, conn);
                daTK.Fill(dtTK);
            }
            else if (tbTG.Text != "")
            {
                sqlThongke = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From HOADON WHERE TacGia = '" + tbTG.Text + "'";
                daTK = new SqlDataAdapter(sqlThongke, conn);
                daTK.Fill(dtTK);
            }
            else if (tbNXB.Text != "")
            {
                sqlThongke = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From HOADON WHERE NhaXuatBan = '" + tbNXB.Text + "'";
                daTK = new SqlDataAdapter(sqlThongke, conn);
                daTK.Fill(dtTK);
            }
            else
            {
                sqlThongke = "Select TenSach, Giatien, TheLoai, TacGia, NhaXuatBan From HOADON";
                daTK = new SqlDataAdapter(sqlThongke, conn);
                daTK.Fill(dtTK);
            }

            dgvTK.DataSource = dtTK;
            conn.Close();
        }
    }

 

}
