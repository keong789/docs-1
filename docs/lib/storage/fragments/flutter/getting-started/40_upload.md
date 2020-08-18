```dart
void uploadFile() async {
  // use a file selection mechanism of your choice
  File local = await FilePicker.getFile(type: FileType.image);
  final key = new DateTime.now().toString();
  final path = local.absolute.path;
  UploadFileResult result = await Amplify.Storage.uploadFile(key: key, path: path);
}
```
