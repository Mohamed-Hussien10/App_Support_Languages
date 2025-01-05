# App_Support_Languages
## Steps to Add Arabic Language Support

Follow these steps to enable Arabic language support in the app:

1. **Add localization dependencies**:

   Make sure you have the `flutter_localizations` package added to your `pubspec.yaml` file. It comes pre-installed in Flutter SDK.
   ```yaml
   dependencies:
     flutter_localizations:
       sdk: flutter
   ```

2. **Create a Localization Class**:

   Use the `AppLocalizations` class to manage translations. Refer to the `app_localizations.dart` file in the `components` folder.

3. **Add JSON Files for Translations**:

   Create `en.json` and `ar.json` files in the `assets/languages/` directory and add your translations.

   Example `en.json`:
   ```json
   {
     "settings": "Settings",
     "privacy_policy": "Privacy Policy",
     "language": "Language",
     "select_language": "Select Language",
     "app_version": "App Version",
     "arabic": "Arabic",
     "english": "English",
     "addedToCartMessage": "Added to Cart!",
     "addToCart": "Add to Cart"
   }
   ```

   Example `ar.json`:
   ```json
   {
     "settings": "الإعدادات",
     "privacy_policy": "سياسة الخصوصية",
     "language": "اللغة",
     "select_language": "اختر اللغة",
     "app_version": "إصدار التطبيق",
     "arabic": "العربية",
     "english": "الإنجليزية",
     "addedToCartMessage": "تمت الإضافة إلى السلة!",
     "addToCart": "أضف إلى السلة"
   }
   ```

4. **Add the `Setting.dart` and implement languages**:

   

   ```dart
    import 'package:flutter/material.dart';
    import 'package:nahdy/components/app_localizations.dart';
    import 'package:nahdy/main.dart';
    import 'package:shared_preferences/shared_preferences.dart';
    
    class SettingsPage extends StatelessWidget {
      const SettingsPage({super.key});
    
      @override
      Widget build(BuildContext context) {
        var t = AppLocalizations.of(context).translate;

    return Scaffold(
      appBar: AppBar(
        title: Text(
          t("settings"),
          style: const TextStyle(
              fontSize: 25, fontWeight: FontWeight.bold, color: Colors.white),
        ),
        backgroundColor: Colors.teal,
        centerTitle: true,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            ListTile(
              leading: const Icon(Icons.language, color: Colors.teal),
              title: Text(
                t("language"),
                style: const TextStyle(fontSize: 18),
              ),
              subtitle: Text(
                  Localizations.localeOf(context).languageCode == 'ar'
                      ? t("arabic")
                      : t("english")),
              onTap: () {
                _showLanguageDialog(context);
              },
            ),
          ],
        ),
      ),
    );
   }
  
    void _showLanguageDialog(BuildContext context) {
      var t = AppLocalizations.of(context).translate;

    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text(t("select_language")),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            ListTile(
              title: Text(t("english")),
              onTap: () {
                _changeLanguage(context, const Locale('en'));
              },
            ),
            ListTile(
              title: Text(t("arabic")),
              onTap: () {
                _changeLanguage(context, const Locale('ar'));
              },
            ),
          ],
        ),
      ),
    );
   }

    void _changeLanguage(BuildContext context, Locale newLocale) async {
      // Save the selected language to shared preferences
      SharedPreferences prefs = await SharedPreferences.getInstance();
      await prefs.setString('languageCode', newLocale.languageCode);

      // Change the app locale
      MyApp.of(context).setLocale(newLocale);
      Navigator.pop(context); // Close the dialog
      }
    }

   ```

5. **Update Main.dart**:

   ```dart
    import 'package:flutter/material.dart';
    import 'package:flutter_localizations/flutter_localizations.dart';
    import 'package:shared_preferences/shared_preferences.dart';
    import 'package:nahdy/components/app_localizations.dart';
    import 'package:nahdy/pages/login_page.dart';
    
    Future<void> main() async {
      WidgetsFlutterBinding.ensureInitialized();
      runApp(const MyApp());
    }
    
    class MyApp extends StatefulWidget {
      const MyApp({super.key});
    
      static _MyAppState of(BuildContext context) =>
          context.findAncestorStateOfType<_MyAppState>()!;
    
      @override
      State<MyApp> createState() => _MyAppState();
    }
    
    class _MyAppState extends State<MyApp> {
      Locale _locale = const Locale('en'); // Default language is English
    
      @override
      void initState() {
        super.initState();
        _loadSavedLocale();
      }
    
      // Load the saved language from SharedPreferences
      Future<void> _loadSavedLocale() async {
        SharedPreferences prefs = await SharedPreferences.getInstance();
        String? languageCode =
            prefs.getString('languageCode'); // Retrieve saved language code
        setState(() {
          _locale =
              Locale(languageCode ?? 'en'); // Default to 'en' if no language saved
        });
      }
    
      // Save the selected language in SharedPreferences
      Future<void> _saveLocale(String languageCode) async {
        SharedPreferences prefs = await SharedPreferences.getInstance();
        await prefs.setString('languageCode', languageCode);
      }
    
      // Set the app's locale and save it
      void setLocale(Locale locale) {
        setState(() {
          _locale = locale;
        });
        _saveLocale(locale.languageCode); // Save the selected language
      }
    
      @override
      Widget build(BuildContext context) {
        return MaterialApp(
          title: 'Alnahdi Est',
          debugShowCheckedModeBanner: false,
          locale: _locale,
          localizationsDelegates: const [
            AppLocalizationsDelegate(),
            GlobalMaterialLocalizations.delegate,
            GlobalWidgetsLocalizations.delegate,
            GlobalCupertinoLocalizations.delegate,
          ],
          supportedLocales: const [
            Locale('en', ''), // English
            Locale('ar', ''), // Arabic
          ],
          home: const HomePage(),
        );
      }
    } 
   ```

6. **Test Localization**:

   Run the app and test switching between English and Arabic to ensure translations are displayed correctly.
