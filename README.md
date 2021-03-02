# SalesforceMobileSDK-FlutterPlugin
Flutter plugin for Salesforce Mobile SDK

## Android 

## For Add custom Login URL
Add to your project Android/app/src/main/res/xml/server.xml
```bash
<?xml version="1.0" encoding="utf-8"?>
<servers>
    <server name="Production" url="https://devmobauth-ob2c.cs128.force.com/customers/" />
    <server name="Sandbox" url="https://devmobauth-ob2c.cs128.force.com/customers/" />
</servers>
``` 

## IOS
Working in process

## Getting Started
# 1. Depend on it
Add this to your package's pubspec.yaml file:
```bash
dependencies:
  salesforce:
    git:
      url: git://github.com/Sheikhsoft/Salesforce-FlutterPlugin.git
``` 

# 2. Install it
You can install packages from the command line:
```bash
flutter pub get
``` 

3. Import it
Now in your Dart code, you can use:
```bash
import 'package:salesforce/salesforce.dart';
```

## Example
```bash
import 'package:flutter/material.dart';
import 'package:salesforce/salesforce.dart';

class Contact {
  final String name;
  final String email;

  const Contact({this.name, this.email});
}

class ContactListItem extends ListTile {

  ContactListItem(Contact contact) :
        super(
          title : new Text(contact.name),
          subtitle: new Text(contact.email),
          leading: new CircleAvatar(
              child: new Text(contact.name[0])
          )
      );

}

class ContactList extends StatelessWidget {

  final List<Contact> contacts;

  ContactList(this.contacts);

  @override
  Widget build(BuildContext context) {
    return new ListView.builder(
        padding: new EdgeInsets.symmetric(vertical: 8.0),
        itemBuilder: (BuildContext context, int index) => new ContactListItem(contacts[index]),
        itemCount: contacts.length
    );
  }

}

class ContactsPageState extends State<ContactsPage> {
  List<Contact> contacts;

  @override
  void initState() {
    super.initState();
    setState(() => this.contacts = []);
    fetchData(); // asynchronous
  }

  void fetchData() async {

    try {
      //Query test
      Map response = await SalesforcePlugin.query("SELECT Id, Name, Email FROM Contact LIMIT 10000");
      final List<dynamic> records = response['records'] ?? [];
      final List<Contact> contacts = records.map((record) => new Contact(name: record["Name"] ?? "", email: record["Email"]  ?? "")).toList();
      print('results: ${contacts.length}');
      if (mounted) {
        setState(() => this.contacts = contacts);
      }

    } on Exception catch (e){
      print('Error: ${e.toString()}');
    }
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
        appBar: new AppBar(
          title: new Text("Contacts"),
          actions: [
            IconButton(
              icon: Text("${this.contacts.length}"),
              onPressed: null,
            ),
          ],
        ),
        body: new ContactList(this.contacts),
        floatingActionButton: FloatingActionButton(
          onPressed: () async {
            setState(() => this.contacts = []);
            fetchData(); // asynchronous
          },
          child: Icon(Icons.refresh),
        ),
    );
  }
}

class ContactsPage extends StatefulWidget {

  @override
  ContactsPageState createState() => new ContactsPageState();

}

void main() {
  runApp(
      new MaterialApp(
          title: 'Flutter Demo',
          theme: new ThemeData(
              primarySwatch: Colors.blue
          ),
          home: new ContactsPage(),
      )
  );
}

```






