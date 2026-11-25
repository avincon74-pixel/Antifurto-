# Antifurto: i 10 file da creare su GitHub
Per ogni file: nel repository tocca **Add file > Create new file**, scrivi il nome **esatto** qui sotto (le `/` creano le cartelle da sole), incolla il contenuto del riquadro e premi **Commit changes**.
Crea i file **in ordine**: l’ultimo (`build.yml`) avvia la compilazione.

---

## 1. `settings.gradle.kts`

```kotlin
pluginManagement { repositories { google(); mavenCentral(); gradlePluginPortal() } }
dependencyResolutionManagement { repositories { google(); mavenCentral() } }
rootProject.name = "Antifurto"
include(":app")
```

---

## 2. `build.gradle.kts`

```kotlin
plugins {
    id("com.android.application") version "8.5.2" apply false
    id("org.jetbrains.kotlin.android") version "1.9.24" apply false
}
```

---

## 3. `gradle.properties`

```properties
-e android.useAndroidX=true
org.gradle.jvmargs=-Xmx2g
```

---

## 4. `app/build.gradle.kts`

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
}
android {
    namespace = "it.antifurto.app"
    compileSdk = 34
    defaultConfig {
        applicationId = "it.antifurto.app"
        minSdk = 26
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
    kotlinOptions { jvmTarget = "17" }
}
dependencies {
    implementation("androidx.appcompat:appcompat:1.7.0")
    implementation("androidx.biometric:biometric:1.1.0")
    implementation("com.google.android.material:material:1.12.0")
}
```

---

## 5. `app/src/main/AndroidManifest.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <uses-permission android:name="android.permission.SEND_SMS" />
    <uses-permission android:name="android.permission.RECEIVE_SMS" />
    <uses-feature android:name="android.hardware.telephony" android:required="true" />

    <application
        android:label="Antifurto"
        android:allowBackup="false"
        android:theme="@style/Theme.Antifurto">
        <activity android:name=".MainActivity" android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <receiver android:name=".SmsReceiver" android:exported="true"
            android:permission="android.permission.BROADCAST_SMS">
            <intent-filter>
                <action android:name="android.provider.Telephony.SMS_RECEIVED" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

---

## 6. `app/src/main/res/values/themes.xml`

```xml
<resources>
    <style name="Theme.Antifurto" parent="Theme.Material3.DayNight.NoActionBar" />
</resources>
```

---

## 7. `app/src/main/res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/root"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:fitsSystemWindows="true">
    <LinearLayout
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:orientation="vertical" android:padding="16dp">

        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Antifurto" android:textSize="28sp" android:textStyle="bold" />
        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Centrale Xpanel CSI" android:layout_marginBottom="16dp" />

        <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
            android:orientation="horizontal">
            <Button android:id="@+id/btnOn" android:layout_width="0dp" android:layout_height="96dp"
                android:layout_weight="1" android:layout_margin="4dp" android:text="Accendi" />
            <Button android:id="@+id/btnOff" android:layout_width="0dp" android:layout_height="96dp"
                android:layout_weight="1" android:layout_margin="4dp" android:text="Spegni" />
        </LinearLayout>
        <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
            android:orientation="horizontal">
            <Button android:id="@+id/btnStato" android:layout_width="0dp" android:layout_height="96dp"
                android:layout_weight="1" android:layout_margin="4dp" android:text="Stato sistema" />
            <Button android:id="@+id/btnCheck" android:layout_width="0dp" android:layout_height="96dp"
                android:layout_weight="1" android:layout_margin="4dp" android:text="Check sistema" />
        </LinearLayout>

        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginTop="24dp" android:text="Zone (per Accendi e Spegni)" android:textSize="18sp" android:textStyle="bold" />
        <com.google.android.material.chip.ChipGroup android:id="@+id/chipZones"
            android:layout_width="match_parent" android:layout_height="wrap_content" />
        <TextView android:id="@+id/tvLast" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:layout_marginTop="16dp"
            android:text="Nessun comando inviato." />
        <TextView android:id="@+id/tvReply" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:layout_marginTop="8dp"
            android:textStyle="bold" android:text="Nessuna risposta dalla centrale." />

        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginTop="28dp" android:text="Impostazioni"
            android:textSize="18sp" android:textStyle="bold" />
        <EditText android:id="@+id/etNum" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Numero SIM della centrale"
            android:inputType="phone" />
        <EditText android:id="@+id/etCode" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Codice utente (6 cifre)"
            android:inputType="numberPassword" android:maxLength="6" />
        <EditText android:id="@+id/etOn" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Comando Accendi ({z} = zone)"
            android:inputType="text" />
        <EditText android:id="@+id/etOff" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Comando Spegni ({z} = zone)"
            android:inputType="text" />
        <EditText android:id="@+id/etStato" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Comando Stato sistema"
            android:inputType="text" />
        <EditText android:id="@+id/etCheck" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Comando Check sistema"
            android:inputType="text" />
        <EditText android:id="@+id/etSep" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:hint="Separatore tra zone (vuoto = nessuno)"
            android:inputType="text" />
        <Button android:id="@+id/btnSave" android:layout_width="match_parent"
            android:layout_height="wrap_content" android:layout_marginTop="8dp"
            android:text="Salva impostazioni" />
    </LinearLayout>
</ScrollView>
```

---

## 8. `app/src/main/java/it/antifurto/app/MainActivity.kt`

```kotlin
package it.antifurto.app

import android.Manifest
import android.content.SharedPreferences
import android.os.Build
import android.telephony.SmsManager
import android.view.View
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import android.widget.Toast
import androidx.biometric.BiometricManager
import androidx.biometric.BiometricPrompt
import androidx.core.content.ContextCompat
import com.google.android.material.chip.Chip
import com.google.android.material.chip.ChipGroup
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import java.text.DateFormat
import java.util.Date

class MainActivity : AppCompatActivity() {

    private lateinit var prefs: SharedPreferences
    private val names = mapOf("on" to "Accendi", "off" to "Spegni", "stato" to "Stato sistema", "check" to "Check sistema")
    private val defaults = mapOf("on" to "Accendi {z}", "off" to "Spegni {z}", "stato" to "Stato sistema", "check" to "Check sistema", "sep" to "", "code" to "") // DA VERIFICARE sul manuale
    private var pendingKey: String? = null

    private val listener = SharedPreferences.OnSharedPreferenceChangeListener { _, _ -> showStatus() }

    private val permLauncher = registerForActivityResult(ActivityResultContracts.RequestMultiplePermissions()) { r ->
        if (r.values.all { it }) pendingKey?.let { send(it) }
        else toast("Servono i permessi SMS per usare l’app")
    }

    private fun field(id: Int) = findViewById<EditText>(id)
    private val fieldIds get() = mapOf("on" to R.id.etOn, "off" to R.id.etOff, "stato" to R.id.etStato, "check" to R.id.etCheck,
        "sep" to R.id.etSep, "code" to R.id.etCode)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        prefs = getSharedPreferences("antifurto", MODE_PRIVATE)

        field(R.id.etNum).setText(prefs.getString("num", ""))
        fieldIds.forEach { (k, id) -> field(id).setText(prefs.getString(k, defaults[k])) }

        findViewById<Button>(R.id.btnSave).setOnClickListener {
            val e = prefs.edit().putString("num", field(R.id.etNum).text.toString().replace(" ", ""))
            fieldIds.forEach { (k, id) -> e.putString(k, field(id).text.toString()) }
            e.apply()
            toast("Impostazioni salvate")
        }

        val group = findViewById<ChipGroup>(R.id.chipZones)
        "0123456789AB".forEach { c ->
            group.addView(Chip(this).apply { text = c.toString(); isCheckable = true })
        }

        mapOf(R.id.btnOn to "on", R.id.btnOff to "off", R.id.btnStato to "stato", R.id.btnCheck to "check")
            .forEach { (id, key) -> findViewById<Button>(id).setOnClickListener { request(key) } }

        showStatus()
    }

    override fun onStart() {
        super.onStart()
        prefs.registerOnSharedPreferenceChangeListener(listener)
        showStatus()
        lockIfNeeded()
    }
    override fun onStop() {
        super.onStop()
        prefs.unregisterOnSharedPreferenceChangeListener(listener)
        if (!isChangingConfigurations) unlocked = false
    }

    private var unlocked = false
    private var prompting = false
    private val root get() = findViewById<View>(R.id.root)

    /** All’apertura (e dopo ogni uscita dall’app) il contenuto resta nascosto finché non verifichi l’impronta. */
    private fun lockIfNeeded() {
        if (unlocked || prompting) return
        prompting = true
        root.visibility = View.INVISIBLE
        authenticate("Apri Antifurto", onError = { prompting = false; finish() }) {
            prompting = false
            unlocked = true
            root.visibility = View.VISIBLE
        }
    }

    private fun request(key: String) {
        if (prefs.getString("num", "").isNullOrBlank()) { toast("Imposta prima il numero della centrale"); return }
        if (!Regex("[0-9]{6}").matches(prefs.getString("code", "") ?: "")) { toast("Imposta il codice di 6 cifre"); return }
        if ((key == "on" || key == "off") && selectedZones().isEmpty()) { toast("Scegli almeno una zona"); return }
        if (key != "stato" && key != "check") {
            AlertDialog.Builder(this)
                .setMessage("Inviare “${names[key]}” alla centrale?\nSMS: ${body(key)}")
                .setPositiveButton("Invia") { _, _ -> authenticate(names[key] ?: "") { ensurePermissionsThenSend(key) } }
                .setNegativeButton("Annulla", null)
                .show()
        } else ensurePermissionsThenSend(key)
    }

    /** Impronta (o PIN/sequenza del telefono). Se il telefono non ha alcun blocco, prosegue senza verifica. */
    private fun authenticate(subtitle: String, onError: () -> Unit = {}, onOk: () -> Unit) {
        val auth = BiometricManager.Authenticators.BIOMETRIC_WEAK or BiometricManager.Authenticators.DEVICE_CREDENTIAL
        if (BiometricManager.from(this).canAuthenticate(auth) != BiometricManager.BIOMETRIC_SUCCESS) {
            toast("Nessun blocco schermo configurato: verifica non disponibile")
            onOk()
            return
        }
        val prompt = BiometricPrompt(this, ContextCompat.getMainExecutor(this),
            object : BiometricPrompt.AuthenticationCallback() {
                override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) = onOk()
                override fun onAuthenticationError(errorCode: Int, errString: CharSequence) = onError()
            })
        prompt.authenticate(
            BiometricPrompt.PromptInfo.Builder()
                .setTitle("Conferma con impronta")
                .setSubtitle(subtitle)
                .setAllowedAuthenticators(auth)
                .build()
        )
    }

    private fun ensurePermissionsThenSend(key: String) {
        pendingKey = key
        permLauncher.launch(arrayOf(Manifest.permission.SEND_SMS, Manifest.permission.RECEIVE_SMS))
    }

    private fun send(key: String) {
        val num = prefs.getString("num", "") ?: return
        val text = textFor(key)
        if (body(key).isBlank()) { toast("Imposta il testo SMS per ${names[key]}"); return }
        try {
            val sm = if (Build.VERSION.SDK_INT >= 31) getSystemService(SmsManager::class.java)
            else @Suppress("DEPRECATION") SmsManager.getDefault()
            sm.sendTextMessage(num, null, text, null, null)
            prefs.edit().putString("lastCmd", names[key]).putLong("lastCmdTime", System.currentTimeMillis()).apply()
            toast("Comando inviato")
        } catch (e: Exception) {
            toast("Invio non riuscito: ${e.message}")
        }
    }

    private fun selectedZones(): List<String> {
        val group = findViewById<ChipGroup>(R.id.chipZones)
        return (0 until group.childCount).map { group.getChildAt(it) as Chip }.filter { it.isChecked }.map { it.text.toString() }
    }

    private fun body(key: String): String {
        val t = prefs.getString(key, "") ?: ""
        if (key != "on" && key != "off") return t
        val z = selectedZones().joinToString(prefs.getString("sep", "") ?: "")
        return if (t.contains("{z}")) t.replace("{z}", z) else t + z
    }

    /** Formato SMS: codice (6 cifre) + # + comando (+ zone per Accendi/Spegni). */
    private fun textFor(key: String): String {
        val code = prefs.getString("code", "") ?: ""
        return "$code#" + body(key)
    }

    private fun showStatus() {
        val fmt = DateFormat.getDateTimeInstance(DateFormat.SHORT, DateFormat.SHORT)
        prefs.getString("lastCmd", null)?.let {
            findViewById<TextView>(R.id.tvLast).text = "Ultimo comando: $it, ${fmt.format(Date(prefs.getLong("lastCmdTime", 0)))}"
        }
        prefs.getString("lastReply", null)?.let {
            findViewById<TextView>(R.id.tvReply).text = "Risposta (${fmt.format(Date(prefs.getLong("lastReplyTime", 0)))}): $it"
        }
    }

    private fun toast(t: String) = Toast.makeText(this, t, Toast.LENGTH_SHORT).show()
}
```

---

## 9. `app/src/main/java/it/antifurto/app/SmsReceiver.kt`

```kotlin
package it.antifurto.app

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.provider.Telephony

/** Salva l’ultimo SMS ricevuto dal numero della centrale. */
class SmsReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        if (intent.action != Telephony.Sms.Intents.SMS_RECEIVED_ACTION) return
        val prefs = context.getSharedPreferences("antifurto", Context.MODE_PRIVATE)
        val num = prefs.getString("num", "")?.filter { it.isDigit() } ?: ""
        if (num.length < 6) return
        val msgs = Telephony.Sms.Intents.getMessagesFromIntent(intent)
        val from = msgs.firstOrNull()?.originatingAddress?.filter { it.isDigit() } ?: return
        if (from.takeLast(9) != num.takeLast(9)) return
        val body = msgs.joinToString("") { it.messageBody ?: "" }
        prefs.edit().putString("lastReply", body).putLong("lastReplyTime", System.currentTimeMillis()).apply()
    }
}
```

---

## 10. `.github/workflows/build.yml`

```yaml
name: Build APK

on:
  workflow_dispatch:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Compila l'APK
        run: gradle assembleDebug --no-daemon

      - uses: actions/upload-artifact@v4
        with:
          name: antifurto-apk
          path: app/build/outputs/apk/debug/app-debug.apk
```
