# pmemkv-java
Java bindings for pmemkv

*This is experimental pre-release software and should not be used in
production systems. APIs and file formats may change at any time without
preserving backwards compatibility. All known issues and limitations
are logged as GitHub issues.*

## Dependencies

* Java 8 or higher
* [PMDK](https://github.com/pmem/pmdk) - native persistent memory libraries
* [pmemkv](https://github.com/pmem/pmemkv) - native key/value library
* [pmemkv-jni](https://github.com/pmem/pmemkv-jni) - JNI integration library
* Used only for development & testing:
  * [Apache Maven](https://maven.apache.org) - build system
  * [JUnit](http://junit.org/) - automated test framework
  * [Oleaster Matcher](https://github.com/mscharhag/oleaster/tree/master/oleaster-matcher) - test condition matching library

## Installation

Start by installing [pmemkv](https://github.com/pmem/pmemkv/blob/master/INSTALLING.md) on your system.

Next install [pmemkv-jni](https://github.com/pmem/pmemkv-jni).

It may be necessary to [configure a proxy](https://maven.apache.org/guides/mini/guide-proxies.html) and set `JAVA_HOME` & `JAVA_TOOL_OPTIONS` environment variables:

```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF-8
```

Clone the pmemkv-java tree:

```
git clone https://github.com/pmem/pmemkv-java.git
cd pmemkv-java
```

Finish by installing these bindings:

```
LD_LIBRARY_PATH=path_to_your_libs mvn install
```

## Testing

This library includes a set of automated tests that exercise all functionality.

```
LD_LIBRARY_PATH=path_to_your_libs mvn test
```

## Example

We are using `/dev/shm` to
[emulate persistent memory](http://pmem.io/2016/02/22/pm-emulation.html)
in this simple example.

```java
import io.pmem.pmemkv.Database;

public class Example {
    public static void main(String[] args) {
        System.out.println("Starting engine");
        Database db = new Database("vsmap", "{\"path\":\"/dev/shm\", \"size\":1073741824}");

        System.out.println("Putting new key");
        db.put("key1", "value1");
        assert db.countAll() == 1;

        System.out.println("Reading key back");
        assert db.get("key1").equals("value1");

        System.out.println("Iterating existing keys");
        db.put("key2", "value2");
        db.put("key3", "value3");
        db.getKeys((String k) -> System.out.println("  visited: " + k));

        System.out.println("Removing existing key");
        db.remove("key1");
        assert !db.exists("key1");

        System.out.println("Stopping engine");
        db.stop();
    }
}
```
