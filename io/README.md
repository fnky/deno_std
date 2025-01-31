# std/io

## Bufio

**Uses:**

### readLines

Read reader[like file], line by line:

```ts title="readLines"
import { readLines } from "https://deno.land/std@$STD_VERSION/io/mod.ts";
import * as path from "https://deno.land/std@$STD_VERSION/path/mod.ts";

const filename = path.join(Deno.cwd(), "std/io/README.md");
let fileReader = await Deno.open(filename);

for await (let line of readLines(fileReader)) {
  console.log(line);
}
```

### readStringDelim

Read reader`[like file]` chunk by chunk, splitting based on delimiter.

```ts title="readStringDelim"
import { readStringDelim } from "https://deno.land/std@$STD_VERSION/io/mod.ts";
import * as path from "https://deno.land/std@$STD_VERSION/path/mod.ts";

const filename = path.join(Deno.cwd(), "std/io/README.md");
let fileReader = await Deno.open(filename);

for await (let line of readStringDelim(fileReader, "\n")) {
  console.log(line);
}
```

## Reader

### StringReader

Create a `Reader` object for `string`.

```ts
import { StringReader } from "https://deno.land/std@$STD_VERSION/io/mod.ts";

const data = new Uint8Array(6);
const r = new StringReader("abcdef");
const res0 = await r.read(data);
const res1 = await r.read(new Uint8Array(6));

// Number of bytes read
console.log(res0); // 6
console.log(res1); // null, no byte left to read. EOL

// text

console.log(new TextDecoder().decode(data)); // abcdef
```

**Output:**

```text
6
null
abcdef
```

## Writer

### StringWriter

Create a `Writer` object for `string`.

```ts
import {
  copy,
  copyN,
  StringReader,
  StringWriter,
} from "https://deno.land/std@$STD_VERSION/io/mod.ts";

const w = new StringWriter("base");
const r = new StringReader("0123456789");
await copyN(r, w, 4); // copy 4 bytes

// Number of bytes read
console.log(w.toString()); //base0123

await copy(r, w); // copy all
console.log(w.toString()); // base0123456789
```

**Output:**

```text
base0123
base0123456789
```

## Streams

### readerFromStreamReader

Creates a `Reader` from a `ReadableStreamDefaultReader`.

```ts
import {
  copy,
  readerFromStreamReader,
} from "https://deno.land/std@$STD_VERSION/io/mod.ts";
const res = await fetch("https://deno.land");
const file = await Deno.open("./deno.land.html", { create: true, write: true });

const reader = readerFromStreamReader(res.body!.getReader());
await copy(reader, file);
file.close();
```

### writerFromStreamWriter

Creates a `Writer` from a `WritableStreamDefaultWriter`.

```ts
import {
  copy,
  writerFromStreamWriter,
} from "https://deno.land/std@$STD_VERSION/io/mod.ts";
const file = await Deno.open("./deno.land.html", { read: true });

const writableStream = new WritableStream({
  write(chunk): void {
    console.log(chunk);
  },
});
const writer = writerFromStreamWriter(writableStream.getWriter());
await copy(file, writer);
file.close();
```
