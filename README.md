<!DOCTYPE html>
<html>
    <head>
        <title>Example</title>
    </head>
    <body>

===========db_koneksi==============

package koneksi;

import java.sql.*;
import java.sql.Connection;
import javax.swing.JOptionPane;

public class db_koneksi {
    private static Connection conn;
    public static Connection getKoneksi(){
        String host  = "jdbc:mysql://localhost:3306/db_karyawan",
                user = "root",
                pass = "";
                
        try{
            conn = (Connection) DriverManager.getConnection(host,user,pass);         
        }catch (SQLException err){
            JOptionPane.showMessageDialog(null, err.getMessage());
        }
        return conn;
    }
    public static void main(String[] args) {
        
    }
}

=======================
package karyawan;
import java.sql.*;
import javax.swing.JOptionPane;
import javax.swing.table.DefaultTableModel;
import koneksi.db_koneksi;
import com.mysql.jdbc.Statement;

public class formGaji extends javax.swing.JFrame {
    
    private DefaultTableModel model;
    String nip, nama,jabatan;
    int gajiPokok,transport,gajiBersih;

    public formGaji() {
        initComponents();
        
        model = new DefaultTableModel();
        tableData.setModel (model);
        model.addColumn ("NIP");
        model.addColumn ("Nama");
        model.addColumn ("Jabatan");
        model.addColumn ("Gaji Pokok");
        model.addColumn ("Transport");
        model.addColumn ("Gaji Bersih");

        getData();
    }
    
    public final void getData() {
        model.getDataVector().removeAllElements();
        model.fireTableDataChanged();
        
        try {
            Statement stat = (Statement) db_koneksi.getKoneksi().createStatement();
            String sql = "SELECT * FROM table_gaji";
            ResultSet rs = stat.executeQuery(sql);

        
        while(rs.next()) {
            Object[] obj = new Object[6];
            obj [0]= rs.getString("nip");
            obj [1]= rs.getString("nama");
            obj [2]= rs.getString("jabatan");
            obj [3]= rs.getString("gajipokok");
            obj [4]= rs.getString("transport");
            obj [5] = rs.getString("gajibersih");
            model.addRow(obj);
            }
        }catch (SQLException err){
            JOptionPane.showMessageDialog(null, err.getMessage());
        }
    }   
        public void loadData() {
            nip = txtNip.getText();
            nama = txtNama.getText();
            jabatan = (String)txtJabatan.getSelectedItem();
            gajiPokok = Integer.parseInt(txtGapok.getText());
            transport=Integer.parseInt(txtTransport.getText());
            gajiBersih = Integer.parseInt(txtGaber.getText());
        }
        
        public void loadGaji () {
            jabatan = "" + txtJabatan.getSelectedItem();
            switch (jabatan) {
                case "Manager":
                    gajiPokok = 5000000;
                    break;
                case "Asistent Manager": 
                    gajiPokok = 4000000;
                    break;
                case "HRD":
                    gajiPokok = 3500000;
                    break;
                case "Karyawan": 
                    gajiPokok = 2700000;
                    break;
                case "Office Boy": 
                    gajiPokok = 2300000;
                    break;
        }
            
        transport = (int) (gajiPokok * 0.1);
        gajiBersih =  gajiPokok + transport; 
        txtGapok.setText("" + gajiPokok);
        txtTransport.setText("" + transport); 
        txtGaber.setText("" + gajiBersih);
    }
        
    public void saveData(){
        loadData ();
        try{
        Statement stat = (Statement)db_koneksi.getKoneksi().createStatement();
        String sql     = "INSERT INTO table_gaji (nip, nama, jabatan, gajipokok, transport, gajibersih)"
                       + " VALUES('" + nip + "','" + nama +"','" + jabatan +"','" 
                       + gajiPokok +"','"+transport +"','"+ gajiBersih +"')";
            PreparedStatement p = (PreparedStatement) db_koneksi.getKoneksi().prepareStatement(sql);
            p.executeUpdate();
            getData();
        } catch (SQLException err){
            JOptionPane.showMessageDialog(null, err.getMessage());
        }
    }
    
    public void Reset () {
        nip="";
        nama="";
        jabatan="";
        gajiPokok = 0;
        transport = 0;
        gajiBersih= 0;
        txtNip.setText (nip);
        txtNama.setText (nama);
        txtGapok.setText("");
        txtTransport.setText("");
        txtGaber.setText("");
    }
    
    public void dataSelect(){
        int i = tableData.getSelectedRow();
        if (i == -1) {
            return;
        }
        txtNip.setText(""+model.getValueAt (i, 0)); 
        txtNama.setText(""+model.getValueAt (i, 1));
        txtJabatan.setSelectedItem(" "+model.getValueAt (i, 2));
        txtGapok.setText(""+model.getValueAt (i, 3)); 
        txtTransport.setText(""+model.getValueAt (i, 4));
        txtGaber.setText(""+model.getValueAt (i, 5));
    }
    
    public void updateData(){
    loadData();
    try {
        Statement stat = (Statement)db_koneksi.getKoneksi().createStatement ();
        String sql = "UPDATE table_gaji SET nama = '"+nama + "',"
                                            + "jabatan = '"+jabatan + "',"
                                            + "gajipokok = '"+gajiPokok + "',"
                                            + "transport = '"+transport + "',"
                                            + "gajibersih = '"+gajiBersih +"'"
                                            +"WHERE nip = '"+nip+"'";

        PreparedStatement p = (PreparedStatement) db_koneksi.getKoneksi().prepareStatement(sql);
        p.executeUpdate();
        getData();
        Reset();
        JOptionPane.showMessageDialog(null, "Update Berhasil ");
        }catch (SQLException err) {
            JOptionPane.showMessageDialog(null, err.getMessage());
        }
    }
    
    public void deleteData(){
        loadData();
        int pesan = JOptionPane.showConfirmDialog(null, "akin mau menghapus Data" + nip +"?", "Konfirmani",
                    JOptionPane.OK_CANCEL_OPTION);
        if (pesan == JOptionPane.OK_OPTION){
            try {
                Statement stat = (Statement) db_koneksi.getKoneksi().createStatement ();
                String sql = "DELETE FROM table_gaji WHERE nip='"+ nip +"'";
                PreparedStatement p = (PreparedStatement) db_koneksi.getKoneksi().prepareStatement(sql);
                p.executeUpdate();
                getData();
                Reset();
                JOptionPane.showMessageDialog(null, "Delete berhasdil");
            }catch (SQLException err) {
                JOptionPane.showMessageDialog(null, err.getMessage());
            }
        }
    }




==================================

variable nya:

txtNip
txtNama
txtGapok
txtTransport
txtGaber
txtJabatan
tableData (untuk tabelnya)

buat button nya:

save
reset
update
dell
exit

jabatan nya:

Manager
Asistent Manager
HRD
Karyawan
Office Boy

===============================

nama projek = CRUD_KARYAWAN
nama paket = karyawan & koneksi
nama file = karyawan : formGaji.java
	    koneksi : db_koneksi.java

library: mysql JBDC Driver

nama database = db_karyawan nip(varchar12), nama(varchar50), jabatan(varchar50), gajipokok(int11), transport(int11), gajibersih(int11)

    </body>
</html>
