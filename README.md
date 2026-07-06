using MySql.Data.MySqlClient;

namespace Punto
{
    public class Conexion
    {
        private string cadenaConexion =
            "Server=localhost;" +
            "Database=PuntoDB;" +
            "User ID=root;" +
            "Password=1234;" +
            "Port=3306;" +
            "SslMode=none;";

        public MySqlConnection ObtenerConexion()
        {
            MySqlConnection conexion = new MySqlConnection(cadenaConexion);
            conexion.Open();
            return conexion;
        }
    }
}


private void btnIngresar_Click(object sender, EventArgs e)
{
    if (txtUsuario.Text.Trim() == "" || txtPassword.Text.Trim() == "")
    {
        MessageBox.Show("Complete todos los campos.");
        return;
    }

    try
    {
        Conexion cn = new Conexion();

        using (MySqlConnection conexion = cn.ObtenerConexion())
        {
            string consulta = @"SELECT nombre_completo
                                FROM usuarios
                                WHERE username=@usuario
                                AND password=@password";

            MySqlCommand cmd = new MySqlCommand(consulta, conexion);

            cmd.Parameters.AddWithValue("@usuario", txtUsuario.Text);
            cmd.Parameters.AddWithValue("@password", txtPassword.Text);

            object resultado = cmd.ExecuteScalar();

            if (resultado != null)
            {
                MessageBox.Show("Bienvenido " + resultado.ToString());

                FrmPrincipal frm = new FrmPrincipal();
                frm.Show();

                this.Hide();
            }
            else
            {
                MessageBox.Show("Usuario o contraseña incorrectos.");
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show("Error de conexión: " + ex.Message);
    }
}




using MySql.Data.MySqlClient;
using System.Data;

Conexion cn = new Conexion();

int idProducto = 0;



private void CargarProductos()
{
    try
    {
        using (MySqlConnection conexion = cn.ObtenerConexion())
        {
            string consulta = "SELECT * FROM productos";

            MySqlDataAdapter da =
                new MySqlDataAdapter(consulta, conexion);

            DataTable dt = new DataTable();

            da.Fill(dt);

            dgvProductos.DataSource = dt;
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
}


private void btnGuardar_Click(object sender, EventArgs e)
{
    decimal precio;
    int stock;

    if (!decimal.TryParse(txtPrecio.Text, out precio))
    {
        MessageBox.Show("Precio inválido");
        return;
    }

    if (!int.TryParse(txtStock.Text, out stock))
    {
        MessageBox.Show("Stock inválido");
        return;
    }

    try
    {
        using (MySqlConnection conexion = cn.ObtenerConexion())
        {
            string consulta = @"INSERT INTO productos
                              (codigo,descripcion,precio,stock)
                              VALUES
                              (@codigo,@descripcion,@precio,@stock)";

            MySqlCommand cmd =
                new MySqlCommand(consulta, conexion);

            cmd.Parameters.AddWithValue("@codigo", txtCodigo.Text);
            cmd.Parameters.AddWithValue("@descripcion", txtDescripcion.Text);
            cmd.Parameters.AddWithValue("@precio", precio);
            cmd.Parameters.AddWithValue("@stock", stock);

            cmd.ExecuteNonQuery();

            MessageBox.Show("Producto guardado.");

            CargarProductos();
            Limpiar();
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
}

