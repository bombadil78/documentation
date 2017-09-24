# Idioms

## Read from console

## Reading a text file

// Java 8
try (Stream<String> s = Files.lines(Paths.get("somePath"))) {
	// do sth with stream
} catch (IOException ex) {
	ex.printStackTrace();
}

// Before Java 8 (using Scanner)
Scanner scanner = new Scanner(new FileInputStream("someFile.txt"));
while (scanner.hasNextLine()) {
	String line = scanner.nextLine();
}

// Before Java 8 (using Readers)
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream("someFile.txt")));
String line = bufferedReader.readLine();
while (line != null) {
	// do sth with line
}

## File operations
- Path
  * get(String... parts): Creates absolute or relative path
  * pathOne.resolve(pathTwo): Combines paths (pathTwo == absolute then pathTwo else pathOne concat pathTwo)
  * pathOne.relativize(pathTwo): Expresses way from one to two as Path (relative)
- Files
  * readAllBytes(): reads binary data
  * lines(): read Stream of Strings
  *  write(toPath, someBytes), e.g. write(toPath, textAsString.getBytes(StandardCharsets.UTF_8)) to write text data

## Date and Time (Java 8)
- Current time
- Current day
- Start new threads (Java 8)
