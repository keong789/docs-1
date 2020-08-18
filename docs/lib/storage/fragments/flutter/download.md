If you uploaded the data using the key `ExampleKey`, you can retrieve the data using `Amplify.Storage.downloadFile`.

```dart
Future<String> get _localPath async {
  final directory = await getApplicationDocumentsDirectory();
  return directory.path;
}

Future<File> get _localFile async {
  final path = await _localPath;
  return File('$path/destinationpath.png');
}

try {
  DownloadFileResult result = await Amplify.Storage.downloadFile(
    key: 'ExampleKey',
    local: _localFile,
  );
} catch (e) {
  print(e.toString());
}
```
