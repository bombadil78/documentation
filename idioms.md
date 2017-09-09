# Idioms

## Read from console

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
