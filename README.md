# Pemrograman-Bergerak
#MainActivity.java
package com.example.crud;
import android.content.DialogInterface;
import android.content.Intent;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import com.google.android.material.floatingactionbutton.FloatingActionButton;
import com.google.android.material.snackbar.Snackbar;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.appcompat.widget.Toolbar;
import android.util.Log;
import android.view.View;
import android.view.Menu;
import android.view.MenuItem;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
public class MainActivity extends AppCompatActivity {
	String[] daftar;
	ListView ListView01;
	Menu menu;
	protected Cursor cursor;
	DataHelper dbcenter;
	public static MainActivity ma;
	@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.activity_main);
	Button btn=(Button)findViewById(R.id.button2);
	btn.setOnClickListener(new View.OnClickListener() {
		@Override
		public void onClick(View arg0) {
		Intent inte = new Intent(MainActivity.this, BuatBiodata.class);
		Log.d("11", "kudune ora error");
		startActivity(inte);
		}
	});
	ma = this;
	dbcenter = new DataHelper(this);
	RefreshList();
}
public void RefreshList() {
	SQLiteDatabase db = dbcenter.getReadableDatabase();
	cursor = db.rawQuery("SELECT * FROM biodata", null);
	daftar = new String[cursor.getCount()];
	cursor.moveToFirst();
	for (int cc = 0; cc < cursor.getCount(); cc++) {
		cursor.moveToPosition(cc);
		daftar[cc] = cursor.getString(1).toString();
	}
	ListView01 = (ListView) findViewById(R.id.listView);
	ListView01.setAdapter(new ArrayAdapter(this, android.R.layout.simple_expandable_list_item_1, daftar));
	ListView01.setSelected(true);
	ListView01.setOnItemClickListener(new AdapterView.OnItemClickListener() {
		@Override
		public void onItemClick(AdapterView arg0, View arg1, int arg2, long arg3) {
		final String selection = daftar[arg2];
		final CharSequence[] dialogitem = {"Lihat Biodata", "Update Biodata", "Hapus Biodata"};
		AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
		builder.setTitle("Pilihan");
		builder.setItems(dialogitem, new DialogInterface.OnClickListener() {
			@Override
			public void onClick(DialogInterface dialog, int item) {
				switch (item) {
					case 0:
						Intent i = new Intent(getApplicationContext(), LihatBiodata.class);
						i.putExtra("nama", selection);
						startActivity(i);
						break;
					case 1:
						Intent in = new Intent(getApplicationContext(),UpdateBiodata.class);
						in.putExtra("nama", selection);
						startActivity(in);
						break;
					case 2:
						SQLiteDatabase db = dbcenter.getWritableDatabase();
						db.execSQL("delete from biodata where nama = '" + selection + "'");
						RefreshList();
						break;
				}
			}
		});
		builder.create().show();
	}});
	((ArrayAdapter)ListView01.getAdapter()).notifyDataSetInvalidated();
}
@Override
public boolean onCreateOptionsMenu(Menu menu) {
	getMenuInflater().inflate(R.menu.menu_main, menu);
	return true;
}
@Override
public boolean onOptionsItemSelected(MenuItem item) {
int id = item.getItemId();
if (id == R.id.action_settings) {
	return true;
}
return super.onOptionsItemSelected(item);
}
}

#BuatBiodata.java
package com.example.crud;
import androidx.appcompat.app.AppCompatActivity;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.view.Menu;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
public class BuatBiodata extends AppCompatActivity {
	protected Cursor cursor;
	DataHelper dbHelper;
	Button bt1,bt2;
	EditText text1,text2,text3,text4,text5;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_buat_biodata);
		dbHelper = new DataHelper(this);
		text1=(EditText) findViewById(R.id.editText1);
		text2=(EditText) findViewById(R.id.editText2);
		text3=(EditText) findViewById(R.id.editText3);
		text4=(EditText) findViewById(R.id.editText4);
		text5=(EditText) findViewById(R.id.editText5);
		bt1=(Button) findViewById(R.id.button1);
		bt2=(Button) findViewById(R.id.button2);
		bt1.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View view) {
			SQLiteDatabase db = dbHelper.getWritableDatabase();
			db.execSQL("insert into biodata(no,nama,tgl,jk,alamat) values('" +
				text1.getText().toString()+"','"+
				text2.getText().toString()+"','" +
				text3.getText().toString()+"','"+
				text4.getText().toString()+"','" +								text5.getText().toString()+"')");
			Toast.makeText(getApplicationContext(),"Berhasil", Toast.LENGTH_LONG).show();
			MainActivity.ma.RefreshList();
			finish();
		}
	});
	bt2.setOnClickListener(new View.OnClickListener() {
		@Override
		public void onClick(View view) {
			finish();
		}
	});
}
public boolean onCreateOptionsMenu(Menu menu) {
getMenuInflater().inflate(R.menu.menu_main, menu);return true;
}
}

#LihatBiodata.java
package com.example.crud;
import androidx.appcompat.app.AppCompatActivity;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.view.Menu;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
public class LihatBiodata extends AppCompatActivity {
	protected Cursor cursor;
	DataHelper dbHelper;
	Button bt2;
	TextView text1,text2,text3,text4,text5;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_lihat_biodata);
		dbHelper = new DataHelper(this);
		text1 = (TextView) findViewById(R.id.textView1);
		text2 = (TextView) findViewById(R.id.textView2);
		text3 = (TextView) findViewById(R.id.textView3);
		text4 = (TextView) findViewById(R.id.textView4);
		text5 = (TextView) findViewById(R.id.textView5);
		SQLiteDatabase db = dbHelper.getReadableDatabase();
		cursor = db.rawQuery("SELECT * FROM biodata WHERE nama =  '" +getIntent().getStringExtra("nama") + "'",null);
		cursor.moveToFirst();
		if (cursor.getCount()>0)
		{
			cursor.moveToPosition(0);
			text1.setText(cursor.getString(0).toString());
			text2.setText(cursor.getString(1).toString());
			text3.setText(cursor.getString(2).toString());
			text4.setText(cursor.getString(3).toString());
			text5.setText(cursor.getString(4).toString());
		}
		bt2 = (Button) findViewById(R.id.button1);
		bt2.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View view) {
			finish();
		}
	});
}
@Override
public boolean onCreateOptionsMenu(Menu menu) {
	getMenuInflater().inflate(R.menu.menu_main, menu);
	return true;
	}
}

#UpdateBiodata.java
package com.example.crud;
import androidx.appcompat.app.AppCompatActivity;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.view.Menu;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
public class UpdateBiodata extends AppCompatActivity {
	protected Cursor cursor;
	DataHelper dbHelper;
	Button bt1,bt2;
	EditText text1,text2,text3,text4,text5;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_update_biodata);
		dbHelper = new DataHelper(this);
		text1=(EditText) findViewById(R.id.editText1);
		text2=(EditText) findViewById(R.id.editText2);
		text3=(EditText) findViewById(R.id.editText3);
		text4=(EditText) findViewById(R.id.editText4);
		text5=(EditText) findViewById(R.id.editText5);
		SQLiteDatabase db = dbHelper.getReadableDatabase();
		cursor = db.rawQuery("SELECT * FROM biodata WHERE nama = '" +getIntent().getStringExtra("nama") + "'", null);
		cursor.moveToFirst();
		if (cursor.getCount() > 0) {
			cursor.moveToPosition(0);
			text1.setText(cursor.getString(0).toString());
			text2.setText(cursor.getString(1).toString());
			text3.setText(cursor.getString(2).toString());
			text4.setText(cursor.getString(3).toString());
			text5.setText(cursor.getString(4).toString());
		}
		bt1 = (Button) findViewById(R.id.button1);
		bt2 = (Button) findViewById(R.id.button2);
		bt1.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View arg0) {
				SQLiteDatabase db = dbHelper.getWritableDatabase();
				db.execSQL("update biodata set nama='" +
					text2.getText().toString() + "', tgl='" +
					text3.getText().toString() + "', jk='" +
					text4.getText().toString() + "', alamat='" +
					text5.getText().toString() + "' where no='" +
					text1.getText().toString() + "'");
				Toast.makeText(getApplicationContext(), "Berhasil", Toast.LENGTH_LONG).show();
				MainActivity.ma.RefreshList();
				finish();
			}
		});
		bt2.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View view) {
				finish();
			}
		});
	}
public boolean onCreateOptionMenu(Menu menu) {
getMenuInflater().inflate(R.menu.menu_main, menu);
return true;
}
}
