# DartCore
**DartCore** is a minimalist, yet powerful HTTP server framework for the Dart programming language. It provides a simple API for routing and handling HTTP requests, making it perfect for lightweight web applications, APIs, or microservices.

## Features
- Simple and intuitive routing
- Lightweight and fast
- Minimal dependencies
- Easy to use for building RESTful APIs
- Customizable request handling

## Getting Started

### Installation
First, ensure you have the Dart SDK installed. Then, add `dartcore` to your `pubspec.yaml` file as a dependency.

```yaml
dependencies:
  dartcore:
    git: https://github.com/neoapps-dev/dartcore.git
```

Run `dart pub get` to install dependencies.

### Example Usage

Follow these simple steps to set up your DartCore server.

### 1. Create a New Instance of DartCore
```dart
import 'package:dartcore/dartcore.dart' as dartcore;
final app = dartcore.App();
```

### 2. Add Routes
Use the `route` method to handle incoming HTTP requests. Specify the HTTP method (e.g., `GET`, `POST`, etc.) and the route.

```dart
app.route('GET', '/', (HttpRequest req) {
  req.response
    ..write('Hello, Dart!')
    ..close();
});

app.route('GET', '/hello', (HttpRequest req) {
  req.response
    ..write('Hello World from /hello route!')
    ..close();
});
```

### 3. Start the Server
Use the `start` method to start the server. You can specify the `port` and `address`, or use the default values (`localhost` and `8080`).

```dart
app.start(port: 8080);
```

#### Optional Parameters:
- **`address`**: The server's IP address (default: `localhost`).
- **`port`**: The port on which the server listens (default: `8080`).

### Full Example

Here’s a complete example to get you started:

```dart
import 'dart:io';
import 'package:dartcore/dartcore.dart' as dartcore;

void main() async {
  var app = dartcore.App();

  // Custom 404 Handler
  app.set404((request) {
    request.response
      ..statusCode = HttpStatus.notFound
      ..write('Custom 404 Not Found Handler\n')
      ..close();
  });

  // Custom 500 Handler
  app.set500((request, error) {
    request.response
      ..statusCode = HttpStatus.internalServerError
      ..write('Custom 500 Internal Server Error: $error\n')
      ..close();
  });

  // Middleware for logging
  app.use((request, next) async {
    print('[Middleware] ${request.method} ${request.uri}');
    await next();
  });

  // Route for serving static files
  app.route('GET', '/static/<file>', (request) async {
    var filePath = request.uri.pathSegments[2];
    await app.serveStaticFile(request, 'static/$filePath');
  });

  // Route for handling JSON POST requests
  app.route('POST', '/json', (request) async {
    var jsonData = await app.parseJson(request);
    await app.sendJson(request, {'received': jsonData});
  });

  // Route for handling file uploads
  app.route('POST', '/upload', (request) async {
    await app.parseMultipartRequest(request);
    request.response
      ..statusCode = HttpStatus.ok
      ..write('File uploaded successfully.\n')
      ..close();
  });

  // Start the server
  await app.start(port: 8080);
}

```

### Running the Server
Run your Dart server with:

```bash
dart run
```

Visit `http://localhost:8080/` to see the server in action!

---

## Contributing
Contributions are welcome! Please feel free to submit issues and pull requests to help improve **DartCore**.

---

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.