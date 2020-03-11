# Java 10 New Features

## Local variable type inference
Local variable type inference is a feature in Java 10 that allows the developer to skip the type declaration associated with local variables.

```
var data = new ArrayList<>();
```
Ref: http://openjdk.java.net/projects/amber/LVTIFAQ.html

## JDK Enhancement Proposal in JDK 10

* (JEP 286) Local-Variable Type Inference: Enhances the Java Language to extend type inference to declarations of local variables with initializers. It introduces var to Java, something that is common in other languages.
* (JEP 296) Consolidate the JDK Forest into a Single Repository: Combine the numerous repositories of the JDK forest into a single repository in order to simplify and streamline development.
* (JEP 204) Garbage Collector Interface: Improves the source code isolation of different garbage collectors by introducing a clean garbage collector (GC) interface.
* (JEP 307) Parallel Full GC for G1: Improves G1 worst-case latencies by making the full GC parallel.
* (JEP 301) Application Data-Class Sharing: To improve startup and footprint, this JEP extends the existing Class-Data Sharing ("CDS") feature to allow application classes to be placed in the shared archive.
* (JEP 312) Thread-Local Handshakes: Introduce a way to execute a callback on threads without performing a global VM safepoint. Makes it both possible and cheap to stop individual threads and not just all threads or none.
* (JEP 313) Remove the Native-Header Generator Tool: Remove the javah tool from the JDK since it has been superseded by superior functionality in javac.
* (JEP 314) Additional Unicode Language-Tag Extensions: Enhances java.util.Locale and related APIs to implement additional Unicode extensions of BCP 47 language tags.
* (JEP 316) Heap Allocation on Alternative Memory Devices: Enables the HotSpot VM to allocate the Java object heap on an alternative memory device, such as an NV-DIMM, specified by the user.
* (JEP 317) Experimental Java-Based JIT Compiler: Enables the Java-based JIT compiler, Graal, to be used as an experimental JIT compiler on the Linux/x64 platform.
* (JEP 319) Root Certificates: Provides a default set of root Certification Authority (CA) certificates in the JDK.
* (JEP 322) Time-Based Release Versioning: Revises the version-string scheme of the Java SE Platform and the JDK, and related versioning information, for present and future time-based release models.
