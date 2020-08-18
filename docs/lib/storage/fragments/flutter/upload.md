To upload to S3 from a data object, specify the key and the file to be uploaded. 

```dart
try {
  UploadFileResult result = await Amplify.Storage.uploadFile(
    key: key,
    local: local,
    options: options
  );
} catch (e) {
  print(e.toString());
}
```
