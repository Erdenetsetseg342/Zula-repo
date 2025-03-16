using MySql.Data.MySqlClient;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UrbanVogue_OnlineShop
{
    public class Datenbank
    {
        private MySqlConnection con;
        private string connectionString;

        public Datenbank()
        {
            connectionString = "SERVER=localhost;DATABASE=OnlineShopUrban;UID=root;PASSWORD='';";
            con = new MySqlConnection(connectionString);
        }

        public void newBenutzer(Benutzer benzer)
        {
            oeffnen();
            MySqlCommand comd = con.CreateCommand();
            if (benzer.Name != "")
            {
                comd.CommandText = string.Format("INSERT INTO Benutzer VALUES(NULL, '{0}', '{1}','{2}','{3}'", benzer.Name, benzer.Adresse, benzer.Passwort, benzer.Email);
            }
            else
            {
                comd.CommandText = string.Format("UPDATE Benutzer SET Name ='{0}', Adresse = '{1}, Email = '{2}', Passwort = '{3}'" +
                    "WHERE BenutzerID = '{4}'", benzer.Name, benzer.Adresse, benzer.Email, benzer.Passwort, benzer.BenutzerID);
            }
            comd.ExecuteNonQuery();
            schliessen();
        }

        public void delBenutzer(int id)
        {
            oeffnen();
            MySqlCommand comd = con.CreateCommand();
            comd.CommandText = string.Format("DELETE FROM Benutzer WHERE BenutzerID = {0};", id.ToString());
            comd.ExecuteNonQuery();
            schliessen();
        }

        public List<Benutzer> GetBenutzer()
        {
            List<Benutzer> listBenutzer = new List<Benutzer>();
            oeffnen();
            try
            {
                using (MySqlCommand comd = con.CreateCommand())
                {
                    comd.CommandText = "SELECT * FROM Benutzer ORDER BY BenutzerID";
                    using (MySqlDataReader reader = comd.ExecuteReader())
                    {
                        while (reader.Read())
                        {
                            listBenutzer.Add(new Benutzer(
                                reader.GetInt32("BenutzerID"),
                                reader.GetString("Name"),
                                reader.GetString("Adresse"),
                                reader.GetString("Email"),
                                reader.GetString("Passwort")
                            ));
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Fehler beim Abrufen der Benutzer: " + ex.Message);
            }
            schliessen();
            return listBenutzer;
        }
        public List<Zahlungen> GetZahlungen()
        {
            List<Zahlungen> listZahlungen = new List<Zahlungen>();
            oeffnen();

            try
            {
                using (MySqlCommand comd = con.CreateCommand())
                {
                    comd.CommandText = "SELECT * FROM Benutzer ORDER BY BenutzerID";
                    using (MySqlDataReader reader = comd.ExecuteReader())
                    {
                        while (reader.Read())
                        {
                            listZahlungen.Add(new Zahlungen(
                                reader.GetInt32("BenutzerID"),
                                reader.GetInt32("Name"),
                                reader.GetInt32("Adresse"),
                                reader.GetDecimal("Email")
                            ));
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Fehler beim Abrufen der Zahlung: " + ex.Message);
            }
            schliessen();
            return listZahlungen;
        }

        private void oeffnen()
        {
            if (con.State == System.Data.ConnectionState.Closed)
            {
                try
                {
                    con.Open();
                }
                catch (Exception ex)
                {
                    MessageBox.Show("Datenbankverbindungsfehler: " + ex.Message);
                }
            }
        }

        private void schliessen()
        {
            if (con.State == System.Data.ConnectionState.Open)
            {
                con.Close();
            }
        }
    }
}
