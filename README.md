ETAP 1:package com.example.myapplication;

import androidx.room.Dao;
import androidx.room.Delete;
import androidx.room.Insert;
import androidx.room.Query;
import androidx.room.Update;

import java.util.List;
@Dao
public interface GraDAO {
    @Insert
    public void wstawGreDoBazy(Gra gra);

    @Insert
    public void wstawGryDoBazy(Gra ... gry);

    @Delete
    public void usunZBazy(Gra gra);

    @Update
    public void aktualizujGre(Gra gra);

    @Query("SELECT * from gry")
    public List<Gra> zwrocWszystkieGry();

    @Query("SELECT nazwa from gry where min = :ile")
    public List<String> zwrocNazwyGierDlaLiczbyOsob(int ile);
}
package com.example.myapplication;

import androidx.room.Database;
import androidx.room.RoomDatabase;

@Database(entities = {Gra.class},version = 1)
public abstract class GraDataBase extends RoomDatabase {
    public abstract GraDAO zwrocGryDAO();
}
etaP2:
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingStart="24dp"
    android:paddingEnd="24dp"
    android:paddingTop="16dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:gravity="center"
        android:text="Dodaj nową grę"
        android:textSize="28sp"
        android:textStyle="bold" />

    <EditText
        android:id="@+id/eTName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:gravity="center"
        android:hint="Nazwa"
        android:inputType="text" />

    <EditText
        android:id="@+id/eTMin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:gravity="center"
        android:hint="Minimalna ilość osób"
        android:inputType="number" />

    <EditText
        android:id="@+id/eTMax"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:gravity="center"
        android:hint="Maksymalna ilość osób"
        android:inputType="number" />

    <EditText
        android:id="@+id/eTAge"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:gravity="center"
        android:hint="Kategoria wiekowa"
        android:inputType="number" />

    <Spinner
        android:id="@+id/spinnerCategory"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:entries="@array/kategorie" />

    <Button
        android:id="@+id/btnWyslij"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Wyślij" />

    <Button
        android:id="@+id/btnUpdate"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Aktualizuj"
        android:visibility="invisible" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:layout_marginBottom="8dp"
        android:gravity="center"
        android:text="Twoje gry"
        android:textSize="20sp"
        android:textStyle="bold" />

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:divider="#CCCCCC"
        android:dividerHeight="1dp" />
</LinearLayout>

package com.example.myapplication;

import androidx.room.ColumnInfo;
import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity (tableName = "gry")
public class Gra {
    @PrimaryKey(autoGenerate = true)
    public int id;
    public String nazwa;
    @ColumnInfo(name = "min")
    public int minLiczbaOsob;
    public int maksLiczbaOsob;
    public int kategoriaWiekowa;
    public String kategoriaNazwa;
    public Gra(){

    }
    public Gra(String nazwa, int minLiczbaOsob, int maksLiczbaOsob, int kategoriaWiekowa, String kategoriaNazwa) {
        this.nazwa = nazwa;
        this.minLiczbaOsob = minLiczbaOsob;
        this.maksLiczbaOsob = maksLiczbaOsob;
        this.kategoriaWiekowa = kategoriaWiekowa;
        this.kategoriaNazwa = kategoriaNazwa;
    }
    @Override
    public String toString() {
        return nazwa + " (" + kategoriaNazwa + ")\n"
                + minLiczbaOsob + "-" + maksLiczbaOsob + " osób • od " + kategoriaWiekowa + " lat";
    }
}
ETAP 4:

package com.example.organizerwycieczek;

import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.Spinner;

import androidx.appcompat.app.AppCompatActivity;
import androidx.room.Room;

import java.util.List;

public class MainActivity extends AppCompatActivity {

    private WycieczkaDataBase baza;
    private Spinner spinnerPoziom;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        spinnerPoziom = findViewById(R.id.spinnerPoziom);

        String[] poziomy = {
                "Łatwy",
                "Średni",
                "Trudny",
                "Bardzo trudny"
        };

        ArrayAdapter<String> adapter = new ArrayAdapter<>(
                this,
                android.R.layout.simple_spinner_item,
                poziomy
        );

        adapter.setDropDownViewResource(
                android.R.layout.simple_spinner_dropdown_item
        );

        spinnerPoziom.setAdapter(adapter);

        baza = Room.databaseBuilder(
                getApplicationContext(),
                WycieczkaDataBase.class,
                "wycieczki_db"
        )
        .allowMainThreadQueries()
        .build();

        // Odczyt bazy, aby została utworzona
        List<Wycieczka> lista =
                baza.zwrocWycieczkiDAO()
                .zwrocWszystkieWycieczki();
    }
}
etap 5:

package com.example.organizerwycieczek;

import android.os.Bundle;
import android.widget.EditText;
import android.widget.RadioGroup;
import android.widget.RadioButton;
import android.widget.CheckBox;
import android.widget.Button;
import android.widget.Spinner;
import android.widget.ArrayAdapter;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.room.Room;

public class MainActivity extends AppCompatActivity {

    private EditText eTNazwa, eTMiejsce, eTDni, eTCena;

    private RadioGroup radioRodzaj;

    private CheckBox cbSamochod, cbAutobus, cbPociag;
    private CheckBox cbZwiedzanie, cbJedzenie, cbZdjecia;

    private Button btnWyslij;

    private Spinner spinnerPoziom;

    private WycieczkaDataBase baza;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Pobieranie elementow z XML

        eTNazwa = findViewById(R.id.eTNazwa);
        eTMiejsce = findViewById(R.id.eTMiejsce);
        eTDni = findViewById(R.id.eTDni);
        eTCena = findViewById(R.id.eTCena);

        radioRodzaj = findViewById(R.id.radioRodzaj);

        cbSamochod = findViewById(R.id.cbSamochod);
        cbAutobus = findViewById(R.id.cbAutobus);
        cbPociag = findViewById(R.id.cbPociag);

        cbZwiedzanie = findViewById(R.id.cbZwiedzanie);
        cbJedzenie = findViewById(R.id.cbJedzenie);
        cbZdjecia = findViewById(R.id.cbZdjecia);

        btnWyslij = findViewById(R.id.btnWyslij);

        spinnerPoziom = findViewById(R.id.spinnerPoziom);

        // Spinner

        String[] poziomy = {
                "Łatwy",
                "Średni",
                "Trudny",
                "Bardzo trudny"
        };

        ArrayAdapter<String> adapter = new ArrayAdapter<>(
                this,
                android.R.layout.simple_spinner_item,
                poziomy
        );

        adapter.setDropDownViewResource(
                android.R.layout.simple_spinner_dropdown_item
        );

        spinnerPoziom.setAdapter(adapter);

        // Polaczenie z baza danych

        baza = Room.databaseBuilder(
                getApplicationContext(),
                WycieczkaDataBase.class,
                "wycieczki_db"
        )
                .allowMainThreadQueries()
                .fallbackToDestructiveMigration()
                .build();

        // Dodawanie wycieczki

        btnWyslij.setOnClickListener(v -> {

            String nazwa = eTNazwa.getText().toString().trim();
            String miejsce = eTMiejsce.getText().toString().trim();
            String dni = eTDni.getText().toString().trim();
            String cena = eTCena.getText().toString().trim();

            // Sprawdzanie pustych pol

            if (nazwa.isEmpty()) {
                eTNazwa.setError("Podaj nazwę");
                return;
            }

            if (miejsce.isEmpty()) {
                eTMiejsce.setError("Podaj miejsce");
                return;
            }

            if (dni.isEmpty()) {
                eTDni.setError("Podaj liczbę dni");
                return;
            }

            if (cena.isEmpty()) {
                eTCena.setError("Podaj cenę");
                return;
            }

            int liczbaDni;
            double cenaWycieczki;

            try {
                liczbaDni = Integer.parseInt(dni);
                cenaWycieczki = Double.parseDouble(
                        cena.replace(",", ".")
                );
            } catch (NumberFormatException e) {
                Toast.makeText(this,
                        "Podaj poprawne liczby",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            if (liczbaDni < 1 || cenaWycieczki < 0) {
                Toast.makeText(this,
                        "Podaj poprawną liczbę dni i cenę",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            // Pobieranie RadioGroup

            int id = radioRodzaj.getCheckedRadioButtonId();

            if (id == -1) {
                Toast.makeText(this,
                        "Wybierz rodzaj wycieczki",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            RadioButton wybrany = findViewById(id);

            String rodzaj = wybrany.getText().toString();

            // Pobieranie CheckBox transport

            String transport = "";

            if (cbSamochod.isChecked()) {
                transport += "Samochód ";
            }

            if (cbAutobus.isChecked()) {
                transport += "Autobus ";
            }

            if (cbPociag.isChecked()) {
                transport += "Pociąg ";
            }

            // Pobieranie CheckBox atrakcji

            String atrakcje = "";

            if (cbZwiedzanie.isChecked()) {
                atrakcje += "Zabytki ";
            }

            if (cbJedzenie.isChecked()) {
                atrakcje += "Jedzenie ";
            }

            if (cbZdjecia.isChecked()) {
                atrakcje += "Zdjęcia ";
            }

            // Tworzenie obiektu

            Wycieczka wycieczka = new Wycieczka(
                    nazwa,
                    miejsce,
                    liczbaDni,
                    cenaWycieczki,
                    spinnerPoziom.getSelectedItem().toString(),
                    rodzaj,
                    transport.trim(),
                    atrakcje.trim()
            );

            // Zapisywanie do bazy

            baza.zwrocWycieczkiDAO().wstawWycieczke(wycieczka);

            Toast.makeText(this,
                    "Dodano wycieczkę!",
                    Toast.LENGTH_SHORT).show();

            // Czyszczenie formularza

            eTNazwa.setText("");
            eTMiejsce.setText("");
            eTDni.setText("");
            eTCena.setText("");

            radioRodzaj.clearCheck();

            cbSamochod.setChecked(false);
            cbAutobus.setChecked(false);
            cbPociag.setChecked(false);

            cbZwiedzanie.setChecked(false);
            cbJedzenie.setChecked(false);
            cbZdjecia.setChecked(false);

            spinnerPoziom.setSelection(0);

        });
    }
}
eTAP 6


package com.example.organizerwycieczek;

import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.RadioGroup;
import android.widget.RadioButton;
import android.widget.CheckBox;
import android.widget.Button;
import android.widget.Spinner;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.room.Room;

import java.util.List;

public class MainActivity extends AppCompatActivity {

    private EditText eTNazwa, eTMiejsce, eTDni, eTCena;

    private RadioGroup radioRodzaj;

    private CheckBox cbSamochod, cbAutobus, cbPociag;
    private CheckBox cbZwiedzanie, cbJedzenie, cbZdjecia;

    private Button btnWyslij;

    private Spinner spinnerPoziom;

    private ListView listView;

    private WycieczkaDataBase baza;

    private List<Wycieczka> wycieczkiList;

    private ArrayAdapter<Wycieczka> arrayAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Pobieranie elementow z XML

        eTNazwa = findViewById(R.id.eTNazwa);
        eTMiejsce = findViewById(R.id.eTMiejsce);
        eTDni = findViewById(R.id.eTDni);
        eTCena = findViewById(R.id.eTCena);

        radioRodzaj = findViewById(R.id.radioRodzaj);

        cbSamochod = findViewById(R.id.cbSamochod);
        cbAutobus = findViewById(R.id.cbAutobus);
        cbPociag = findViewById(R.id.cbPociag);

        cbZwiedzanie = findViewById(R.id.cbZwiedzanie);
        cbJedzenie = findViewById(R.id.cbJedzenie);
        cbZdjecia = findViewById(R.id.cbZdjecia);

        btnWyslij = findViewById(R.id.btnWyslij);

        spinnerPoziom = findViewById(R.id.spinnerPoziom);

        listView = findViewById(R.id.listView);

        // Spinner

        String[] poziomy = {
                "Łatwy",
                "Średni",
                "Trudny",
                "Bardzo trudny"
        };

        ArrayAdapter<String> spinnerAdapter = new ArrayAdapter<>(
                this,
                android.R.layout.simple_spinner_item,
                poziomy
        );

        spinnerAdapter.setDropDownViewResource(
                android.R.layout.simple_spinner_dropdown_item
        );

        spinnerPoziom.setAdapter(spinnerAdapter);

        // Polaczenie z baza danych

        baza = Room.databaseBuilder(
                getApplicationContext(),
                WycieczkaDataBase.class,
                "wycieczki_db"
        )
                .allowMainThreadQueries()
                .fallbackToDestructiveMigration()
                .build();

        // Pobieranie wycieczek z bazy

        wycieczkiList = baza.zwrocWycieczkiDAO()
                .zwrocWszystkieWycieczki();

        // Adapter ListView

        arrayAdapter = new ArrayAdapter<>(
                MainActivity.this,
                android.R.layout.simple_list_item_1,
                wycieczkiList
        );

        listView.setAdapter(arrayAdapter);

        // Dodawanie wycieczki

        btnWyslij.setOnClickListener(v -> {

            String nazwa = eTNazwa.getText().toString().trim();
            String miejsce = eTMiejsce.getText().toString().trim();
            String dni = eTDni.getText().toString().trim();
            String cena = eTCena.getText().toString().trim();

            if (nazwa.isEmpty()) {
                eTNazwa.setError("Podaj nazwę");
                return;
            }

            if (miejsce.isEmpty()) {
                eTMiejsce.setError("Podaj miejsce");
                return;
            }

            if (dni.isEmpty()) {
                eTDni.setError("Podaj liczbę dni");
                return;
            }

            if (cena.isEmpty()) {
                eTCena.setError("Podaj cenę");
                return;
            }

            int liczbaDni;
            double cenaWycieczki;

            try {
                liczbaDni = Integer.parseInt(dni);
                cenaWycieczki = Double.parseDouble(
                        cena.replace(",", ".")
                );
            } catch (NumberFormatException e) {
                Toast.makeText(this,
                        "Podaj poprawne liczby",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            if (liczbaDni < 1 || cenaWycieczki < 0) {
                Toast.makeText(this,
                        "Podaj poprawną liczbę dni i cenę",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            // RadioGroup

            int id = radioRodzaj.getCheckedRadioButtonId();

            if (id == -1) {
                Toast.makeText(this,
                        "Wybierz rodzaj wycieczki",
                        Toast.LENGTH_SHORT).show();
                return;
            }

            RadioButton wybrany = findViewById(id);

            String rodzaj = wybrany.getText().toString();

            // Transport

            String transport = "";

            if (cbSamochod.isChecked()) {
                transport += "Samochód ";
            }

            if (cbAutobus.isChecked()) {
                transport += "Autobus ";
            }

            if (cbPociag.isChecked()) {
                transport += "Pociąg ";
            }

            // Atrakcje

            String atrakcje = "";

            if (cbZwiedzanie.isChecked()) {
                atrakcje += "Zabytki ";
            }

            if (cbJedzenie.isChecked()) {
                atrakcje += "Jedzenie ";
            }

            if (cbZdjecia.isChecked()) {
                atrakcje += "Zdjęcia ";
            }

            // Tworzenie obiektu

            Wycieczka wycieczka = new Wycieczka(
                    nazwa,
                    miejsce,
                    liczbaDni,
                    cenaWycieczki,
                    spinnerPoziom.getSelectedItem().toString(),
                    rodzaj,
                    transport.trim(),
                    atrakcje.trim()
            );

            // Zapisywanie do bazy

            baza.zwrocWycieczkiDAO()
                    .wstawWycieczke(wycieczka);

            // Odswiezenie listy

            odswiezListe();

            Toast.makeText(this,
                    "Dodano wycieczkę!",
                    Toast.LENGTH_SHORT).show();

            // Czyszczenie formularza

            wyczysc();

        });

    }

    // Pobieranie aktualnych danych z bazy

    private void odswiezListe() {

        wycieczkiList.clear();

        wycieczkiList.addAll(
                baza.zwrocWycieczkiDAO()
                        .zwrocWszystkieWycieczki()
        );

        arrayAdapter.notifyDataSetChanged();
    }

    // Czyszczenie formularza

    private void wyczysc() {

        eTNazwa.setText("");
        eTMiejsce.setText("");
        eTDni.setText("");
        eTCena.setText("");

        radioRodzaj.clearCheck();

        cbSamochod.setChecked(false);
        cbAutobus.setChecked(false);
        cbPociag.setChecked(false);

        cbZwiedzanie.setChecked(false);
        cbJedzenie.setChecked(false);
        cbZdjecia.setChecked(false);

        spinnerPoziom.setSelection(0);
    }
}
etap 7:

package com.example.organizerwycieczek;

import android.os.Bundle;
import android.widget.EditText;
import android.widget.RadioGroup;
import android.widget.RadioButton;
import android.widget.CheckBox;
import android.widget.Button;
import android.widget.Spinner;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.room.Room;

import java.util.List;

public class MainActivity extends AppCompatActivity {

    EditText eTNazwa, eTMiejsce, eTDni, eTCena;

    RadioGroup radioRodzaj;

    CheckBox cbSamochod, cbAutobus, cbPociag;
    CheckBox cbZwiedzanie, cbJedzenie, cbZdjecia;

    Button btnWyslij, btnUpdate;

    Spinner spinnerPoziom;

    ListView listView;

    WycieczkaDataBase baza;

    List<Wycieczka> wycieczkiList;

    ArrayAdapter<Wycieczka> arrayAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        eTNazwa = findViewById(R.id.eTNazwa);
        eTMiejsce = findViewById(R.id.eTMiejsce);
        eTDni = findViewById(R.id.eTDni);
        eTCena = findViewById(R.id.eTCena);

        radioRodzaj = findViewById(R.id.radioRodzaj);

        cbSamochod = findViewById(R.id.cbSamochod);
        cbAutobus = findViewById(R.id.cbAutobus);
        cbPociag = findViewById(R.id.cbPociag);

        cbZwiedzanie = findViewById(R.id.cbZwiedzanie);
        cbJedzenie = findViewById(R.id.cbJedzenie);
        cbZdjecia = findViewById(R.id.cbZdjecia);

        btnWyslij = findViewById(R.id.btnWyslij);
        btnUpdate = findViewById(R.id.btnUpdate);

        spinnerPoziom = findViewById(R.id.spinnerPoziom);

        listView = findViewById(R.id.listView);

        // Ustawienie opcji poziomu trudności
        String[] poziomy = {
                "Łatwy",
                "Średni",
                "Trudny",
                "Bardzo trudny"
        };

        ArrayAdapter<String> spinnerAdapter = new ArrayAdapter<>(
                this,
                android.R.layout.simple_spinner_item,
                poziomy
        );

        spinnerAdapter.setDropDownViewResource(
                android.R.layout.simple_spinner_dropdown_item
        );

        spinnerPoziom.setAdapter(spinnerAdapter);

        // Utworzenie bazy danych Room
        baza = Room.databaseBuilder(
                getApplicationContext(),
                WycieczkaDataBase.class,
                "wycieczki_db"
        )
                .allowMainThreadQueries()
                .fallbackToDestructiveMigration()
                .build();

        // Pobranie wycieczek z bazy
        wycieczkiList = baza.zwrocWycieczkiDAO()
                .zwrocWszystkieWycieczki();

        // Adapter ListView
        arrayAdapter = new ArrayAdapter<>(
                MainActivity.this,
                android.R.layout.simple_list_item_1,
                wycieczkiList
        );

        listView.setAdapter(arrayAdapter);

        // DODAWANIE WYCIECZKI
        btnWyslij.setOnClickListener(v -> {

            String nazwa = eTNazwa.getText().toString().trim();
            String miejsce = eTMiejsce.getText().toString().trim();
            String dniTekst = eTDni.getText().toString().trim();
            String cenaTekst = eTCena.getText().toString().trim();

            if (nazwa.isEmpty() || miejsce.isEmpty()
                    || dniTekst.isEmpty() || cenaTekst.isEmpty()) {

                Toast.makeText(
                        this,
                        "Uzupełnij wszystkie pola!",
                        Toast.LENGTH_SHORT
                ).show();

                return;
            }

            int dni;
            double cena;

            try {
                dni = Integer.parseInt(dniTekst);
                cena = Double.parseDouble(
                        cenaTekst.replace(",", ".")
                );
            } catch (NumberFormatException e) {

                Toast.makeText(
                        this,
                        "Podaj poprawną liczbę dni i cenę!",
                        Toast.LENGTH_SHORT
                ).show();

                return;
            }

            if (dni < 1 || cena < 0) {

                Toast.makeText(
                        this,
                        "Liczba dni musi być większa od 0, a cena nie może być ujemna!",
                        Toast.LENGTH_SHORT
                ).show();

                return;
            }

            // Pobranie rodzaju wycieczki
            int wybranyId = radioRodzaj.getCheckedRadioButtonId();

            if (wybranyId == -1) {

                Toast.makeText(
                        this,
                        "Wybierz rodzaj wycieczki!",
                        Toast.LENGTH_SHORT
                ).show();

                return;
            }

            RadioButton wybranyRadioButton =
                    findViewById(wybranyId);

            String rodzaj = wybranyRadioButton
                    .getText().toString();

            // Pobranie transportu
            StringBuilder transport = new StringBuilder();

            if (cbSamochod.isChecked()) {
                transport.append("Samochód, ");
            }

            if (cbAutobus.isChecked()) {
                transport.append("Autobus, ");
            }

            if (cbPociag.isChecked()) {
                transport.append("Pociąg, ");
            }

            // Pobranie atrakcji
            StringBuilder atrakcje = new StringBuilder();

            if (cbZwiedzanie.isChecked()) {
                atrakcje.append("Zwiedzanie, ");
            }

            if (cbJedzenie.isChecked()) {
                atrakcje.append("Jedzenie, ");
            }

            if (cbZdjecia.isChecked()) {
                atrakcje.append("Zdjęcia, ");
            }

            String poziom = spinnerPoziom
                    .getSelectedItem().toString();

            // Utworzenie obiektu wycieczki
            Wycieczka wycieczka = new Wycieczka(
                    nazwa,
                    miejsce,
                    dni,
                    cena,
                    poziom,
                    rodzaj,
                    transport.toString(),
                    atrakcje.toString()
            );

            // Zapis do bazy danych
            baza.zwrocWycieczkiDAO()
                    .wstawWycieczke(wycieczka);

            // Odświeżenie listy
            odswiezListe();

            Toast.makeText(
                    this,
                    "Dodano wycieczkę!",
                    Toast.LENGTH_SHORT
            ).show();

            wyczysc();
        });

        // USUWANIE WYCIECZKI PRZEZ DŁUGIE KLIKNIĘCIE
        listView.setOnItemLongClickListener(
                (parent, view, position, id) -> {

                    // Pobranie wycieczki z wybranej pozycji
                    Wycieczka doUsuniecia =
                            wycieczkiList.get(position);

                    // Usunięcie wycieczki z bazy danych
                    baza.zwrocWycieczkiDAO()
                            .usunWycieczke(doUsuniecia);

                    // Odświeżenie ListView
                    odswiezListe();

                    Toast.makeText(
                            MainActivity.this,
                            "Usunięto wycieczkę!",
                            Toast.LENGTH_SHORT
                    ).show();

                    return true;
                }
        );
    }

    // ODŚWIEŻANIE LISTY
    private void odswiezListe() {

        wycieczkiList.clear();

        wycieczkiList.addAll(
                baza.zwrocWycieczkiDAO()
                        .zwrocWszystkieWycieczki()
        );

        arrayAdapter.notifyDataSetChanged();
    }

    // CZYSZCZENIE FORMULARZA
    private void wyczysc() {

        eTNazwa.setText("");
        eTMiejsce.setText("");
        eTDni.setText("");
        eTCena.setText("");

        radioRodzaj.clearCheck();

        cbSamochod.setChecked(false);
        cbAutobus.setChecked(false);
        cbPociag.setChecked(false);

        cbZwiedzanie.setChecked(false);
        cbJedzenie.setChecked(false);
        cbZdjecia.setChecked(false);

        spinnerPoziom.setSelection(0);
    }
}

