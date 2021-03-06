# SendGrid-Java

This Java module allows you to quickly and easily send emails through SendGrid using Java.

[![BuildStatus](https://travis-ci.org/sendgrid/sendgrid-java.png?branch=master)](https://travis-ci.org/sendgrid/sendgrid-java)

### Warning

Version ``2.x.x``, behaves differently in the ``addTo`` method. In the past this method defaulted to using the ``SMTPAPI`` header. Now you must explicitly call the ``addSmtpApiTo`` method. More on the ``SMTPAPI`` section.

```java
// SendGridExample.java
import com.sendgrid.*;

public class SendGridExample {
  public static void main(String[] args) {
    SendGrid sendgrid = new SendGrid("SENDGRID USERNAME", "SENDGRID_PASSWORD");

    SendGrid.Email email = new SendGrid.Email();
    email.addTo("example@example.com");
    email.setFrom("other@example.com");
    email.setSubject("Hello World");
    email.setText("My first email with SendGrid Java!");

    try {
      SendGrid.Response response = sendgrid.send(email);
      System.out.println(response.getMessage());
    }
    catch (SendGridException e) {
      System.err.println(e);
    }
  }
}
```
Compile and run this example with

```bash
$ javac -classpath sendgrid-1.2.1-jar.jar:. SendGridExample.java && java -classpath sendgrid-1.2.1-jar.jar:. SendGridExample
```

## Installation

Choose your installation method - Maven w/ Gradle (recommended) or Jar file.

### via Maven w/ Gradle

Add the following to your build.gradle file in the root of your project.

```groovy
...
dependencies {
  ...
  compile 'com.sendgrid:sendgrid-java:2.0.0'
}

repositories {
  mavenCentral()
}
...
```

Then import the library - in the file appropriate to your Java project.

```java
import com.sendgrid.SendGrid;
```

### via jar file

You can just drop the jar file in. It's a fat jar - it has all the dependencies built in.

[sendgrid-java.jar](https://sendgrid-open-source.s3.amazonaws.com/sendgrid-java/sendgrid-java.jar)

```java
import com.sendgrid.*;
```

## Usage

To begin using this library, initialize the SendGrid object with your SendGrid credentials.

```java
import com.sendgrid.SendGrid;
SendGrid sendgrid = new SendGrid("sendgrid_username", "sendgrid_password");
```

Add your message details.

```java
Email email = new Email();
email.addTo("example@example.com");
email.addToName("Example Guy");
email.setFrom("other@example.com");
email.setSubject("Hello World");
email.setText("My first email through SendGrid");
```

Send it.

```java
sendgrid.send(email);
```

### To

```java
email.addTo("example@example.com");
// or
email.setTo(["other@other.com"]);
```

### From

```java
email.setFrom("other@example.com");
```

### From Name

```java
email.setFromName("Other Dude");
```

### Reply To

```java
email.setReplyTo("no-reply@nowhere.com");
```

### Subject

```java
email.setSubject("Hello World");
```

### Text

```java
email.setText("This is some text of the email.");
```

### Html

```java
email.setHtml("<h1>My first email through SendGrid");
```

### Attachments

```java
email.addAttachment("text.txt", "contents");
// or
email.addAttachment("image.png", new File("./image.png"));
// or
email.addAttachment("text.txt", new InputStream(new File("./file.txt")));
```

### Content IDs

```java
// First, add an attachment
email.addAttachment("image.png", new File("./image.png"));
// Map the name of the attachment to an ID
email.addContentId("image.png", "ID_IN_HTML")
// Map the ID in the HTML
email.setHtml("<html><body>TEXT BEFORE IMAGE<img src=\"cid:ID_IN_HTML\"></img>AFTER IMAGE</body></html>")
```

### Proxy Server Setup

```java
SendGrid sendgrid = new SendGrid("SENDGRID USERNAME", "SENDGRID_PASSWORD");
HttpHost proxy = new HttpHost("server", 3128);
CloseableHttpClient http = HttpClientBuilder.create().setProxy(proxy).setUserAgent("sendgrid/" + sendgrid.getVersion() + ";java").build();
sendgrid.setClient(http);
```

## [X-SMTPAPI](http://sendgrid.com/docs/API_Reference/SMTP_API/index.html)

The mail object extends the SMTPAPI object which is found in [STMAPI-Java](https://github.com/sendgrid/smtpapi-java).

```java
email.getSMTPAPI();
```

### Recipients

```java
email.addSmtpApiTo("email@email.com");
// or
email.addSmtpApiTo(["email@email.com"]);
```


### [Substitutions](http://sendgrid.com/docs/API_Reference/SMTP_API/substitution_tags.html)

```java
email.addSubstitution("key", "value");
// or
email.setSubstitutions("key", ["value1", "value2"]);

JSONObject subs = header.getSubstitutions();
```

### [Unique Arguments](http://sendgrid.com/docs/API_Reference/SMTP_API/unique_arguments.html)

```java
email.addUniqueAarg("key", "value");
// or
Map map = new HashMap<String, String>();
map.put("unique", "value");
email.setUniqueArgs(map);
// or
JSONObject map = new JSONObject();
map.put("unique", "value");
email.setUniqueArgs(map);
// or
email.setUniqueArgs(map);

JSONObject args = email.getUniqueArgs();
```
### [Categories](http://sendgrid.com/docs/API_Reference/SMTP_API/categories.html)

```java
email.addCategory("category");
// or
email.addCategory(["categories"]);
// or
email.setCategories(["category1", "category2"]);
// or
email.setCategories(["category1", category2"]);

String[] cats = email.getCategories();
```

### [Sections](http://sendgrid.com/docs/API_Reference/SMTP_API/section_tags.html)

```java
email.addSection("key", "section");
// or
Map newSec = new HashMap();
newSec.put("-section-", "value");
email.setSections(newSec);
// or
JSONObject newSec = new JSONObject();
newSec.put("-section-", "value");
email.setSections(newSec);

JSONObject sections = email.getSections();
```

### [Filters](http://sendgrid.com/docs/API_Reference/SMTP_API/apps.html)

```java
email.addFilter("filter", "setting", "value");
email.addFilter("filter", "setting", 1);

JSONObject filters = email.getFilters();
```

### Get Headers

```java
String headers = email.jsonString();
```

### Filters/Apps

You can enable and configure Apps.

```java
SendGrid sendgrid = new SendGrid("sendgrid_username", "sendgrid_password");
sendgrid.addTo("example@example.com");
...
sendgrid.addFilter("bcc", "enabled", 1);
sendgrid.addFilter("bcc", "email", "example@example.com");
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Running Tests

The existing tests in the `src/test` directory can be run using gradle with the following command:

```bash
$ ./gradlew test -i
```

## Generating the jar

```bash
$ ./gradlew build
```

## Example App

We have an example app using this library. This can be helpful to get a grasp on implementing it in your own app.

[github.com/scottmotte/sendgrid-java-example](http://github.com/scottmotte/sendgrid-java-example)

## License

Licensed under the MIT License.
