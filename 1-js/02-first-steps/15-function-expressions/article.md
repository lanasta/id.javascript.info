<<<<<<< HEAD:1-js/02-first-steps/15-function-expressions-arrows/article.md
# Expresi fungsi dan panah
=======
# Function expressions
>>>>>>> 2b5ac971c1bd8abe7b17cdcf724afd84799b6cbd:1-js/02-first-steps/15-function-expressions/article.md

Di JavaScript, fungsi bukan "struktur bahasa magis", melaikan satu bentuk nilai spesial.

Syntax yang kita gunakan sebelumnya disebut *Deklarasi Fungsi*:

```js
function sayHi() {
  alert( "Hello" );
}
```

Ada syntax lain untuk membuat fungsi yang disebut *Expresi Fungsi*.

Rupanya seperti ini:

```js
let sayHi = function() {
  alert( "Hello" );
};
```

Di sini, fungsi dibuat dan diisi variabel secara explisit, seperti nilai lain manapun. Tak peduli bagaimana fungsi didefinisi, ia hanya suatu nilai yang disimpan dalam variabel `sayHi`.

Arti dari sampel kode ini sama: "buatlah fungs dan taruhlah di dalam variabel `sayHi`".

Kita bahkan bisa mencetak nilai itu menggunakan `alert`:

```js run
function sayHi() {
  alert( "Hello" );
}

*!*
alert( sayHi ); // menampilkan kode fungsi
*/!*
```

Tolong ingat bahwa baris terakhir tidak menjalankan fungsi, karena tak ada tanda kurung setelah `sayHi`. Ada bahasa pemrograman di mana satu penyebutan nama fungsi menyebabkan exekusi fungsi, tapi JavaScript tak seperti itu.

Di JavaScript, fungsi adalah nilai, jadi kita bisa menghadapinya sebagai nilai. Kode di atas menunjukkan representasi stringnya, yang mana kode sumbernya.

Pastinya, fungsi adalah nilai spesial, dengan anggapan bahwa kita bisa memanggilnya seperti `sayHi()`.

Tapi ia tetaplah nilai. Jadi kita bisa memakainya seperti macam nilai lainnya.

Kita bisa mengkopi fungsi ke variabel lain:

```js run no-beautify
function sayHi() {   // (1) buat
  alert( "Hello" );
}

let func = sayHi;    // (2) kopi

func(); // Hello     // (3) jalankan kopinya (ia bekerja)!
sayHi(); // Hello    //     ini juga masih bekerja (kenapa tidak)
```

Inilah yang terjadi di atas secara detil:

1. Deklarasi Fungsi `(1)` membuat fungsi dan menaruhnya ke variabel bernama `sayHi`.
2. Baris `(2)` mengkopinya ke variabel `func`. Tolong ingat lagi: tak ada tanda kurung setelah `sayHi`. Jika ada, maka `func = sayHi()` akan menulis  *hasil panggilan* `sayHi()` ke `func`, bukan *fungsi* `sayHi` itu sendiri.
3. Sekarang fungsi bisa dipanggil baik sebagai `sayHi()` maupun `func()`.

Catat bahwa kita jusa bisa menggunakan Expresi Fungsi untuk mendeklarasi `sayHi`, di baris pertama:

```js
let sayHi = function() {
  alert( "Hello" );
};

let func = sayHi;
// ...
```

Semua akan berjalan sama.


````smart header="Kenapa ada semicolon di akhir?"
Kamu mungkin penasaran, kenapa Expresi Fungsi punya semicolon `;` di akhir, tapi Deklarasi Fungsi tidak:

```js
function sayHi() {
  // ...
}

let sayHi = function() {
  // ...
}*!*;*/!*
```

Jawabannya simpel:
- `;` tidak dibutuhkan di akhir blok kode dan struktur syntax yang memakai mereka seperti `if { ... }`, `for {  }`, `function f { }` dll.
- Expresi Fungsi digunakan di dalam pernyataan: `let sayHi = ...;`, sebagai nilai. Ia bukan blok kode, tapi lebih ke penetapan. Semicolon `;` disarankan di akhir pernyataan, tak peduli nilainya apa. Jadi semicolon di sini tak ada hubungannya dengan Expresi Fungsi itu sendiri, ia hanya menstop pernyataan.
````

## Fungsi callback

Ayo kita lihat pada contoh lain mengoper fungsi sebagai nilai dan menggunakan expresi fungsi.

Kita akan menulis fungsi `ask(question, yes, no)` dengan tiga parameter:

`question`
: Teks pertanyaan

`yes`
: Fungsi yang berjalan jika jawabannya "Yes"

`no`
: Fungsi yang berjalan jika jawabannya "No"

Fungsinya akan menanyakan `question` dan, tergantung jawabannya pengguna, panggil `yes()` atau `no()`:

```js run
*!*
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}
*/!*

function showOk() {
  alert( "You agreed." );
}

function showCancel() {
  alert( "You canceled the execution." );
}

// usage: functions showOk, showCancel are passed as arguments to ask
ask("Do you agree?", showOk, showCancel);
```

Pada praktiknya, fungsi macam ini agak berguna. Perbedaan besar antara `ask` kehidupan-nyata dan contoh di atas adalah fungsi kehidupan-nyata memakai cara komplex untuk berinteraksi dengan pengguna daripada sekedar `confirm`. Di peramban, fungsi macam ini biasanya menarik window pertanyaan menarik. Tapi itu cerita lain lagi.

**Argumen `showOk` dan `showCancel` dari `ask` dipanggil *fungsi callback* atau hanya *callback*.**

Idenya adalah kita mengoper fungsi dan berharap ia "dipanggil kembali" kemudian jika dibutuhkan. Pada kasus kita, `showOk` menjadi callback untuk jawaban "yes", dan `showCancel` untuk jawaban "no".

Kita bisa memakai Expresi Fungsi untuk menulis fungsi yang sama lebih pendek:

```js run no-beautify
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

*!*
ask(
  "Do you agree?",
  function() { alert("You agreed."); },
  function() { alert("You canceled the execution."); }
);
*/!*
```

Di sini, fungsi dideklarasi tepat di dalam panggilan `ask(...)`. Mereka tak punya nama, jadinya disebut *anonymous*. Fungsi macam ini tak bisa diakses di luar `ask` (karena mereka tak diset ke variabel), tapi itulah yang kita mau di sini.

Kode macam ini muncul di script kita secara sangat alamiah, ia ada di dalam semangat JavaScript.

```smart header="Fungsi adalah nilai yang mewakili \"aksi\""
Nilai reguler seperti string atau angka mewakiliki *data*.

Fungsi bisa dianggap sebagai *aksi*.

Kita bisa mengopernya antara variabel dan menjalankannya kapanpun kita mau.
```


## Expresi Fungsi vs Deklarasi Fungsi

Mari kita rumuskan kunci perbedaan antara Deklarasi dan Expresi Fungsi.

Pertama, syntax: bagaimana membedakan antara mereka dalam kode.

- *Deklarasi Fungsi:* fungsi, yang dideklarasi sebagai pernyataan terpisah, dalam aliran kode utama.

    ```js
    // Deklarasi Fungsi
    function sum(a, b) {
      return a + b;
    }
    ```
- *Expresi Fungsi:* fungsi, yang dibuat dalam expresi atau dalam konstruksi syntax lain. Di sini, fungsi dibuat pada sisi kanan dari "expresi penetapan" `=`:

    ```js
    // Expresi Fungsi
    let sum = function(a, b) {
      return a + b;
    };
    ```

Perbedaan yang lebih halus ialah *ketika* fungsi dibuat oleh JavaScript engine.

**Expresi Fungsi dibuat ketika exekusi mencapainya dan hanya dapat dipakai saat itu saja.**

Sekali aliran exekusi melewati sisi kanan penetapan `let sum = function…` -- di sinilah, fungsi dibuat dan bisa digunakan (diset, dipanggil, dll. ) dari sekarang.

Deklarasi Fungsi berbeda.

**Deklarasi Fungsi bisa dipanggil lebih cepat dari ia didefinisi.**

Misalnya, Deklarasi Fungsi global terlihat di seluruh script, tak peduli di mana ia berada.

Itu karena algoritma internal. Saat JavaScript siap menjalankan script, pertama ia melihat Deklarasi Fungsi global di dalam dan membuat fungsi. Kita bisa anggap itu sebagai "tahap inisialisasi".

Dan setelah semua Deklarasi Fungsi diproses, kodenya diexekusi. Jadi ia punya akses ke fungsi-fungsi ini.

Misalnya, ini berjalan:

```js run refresh untrusted
*!*
sayHi("John"); // Hello, John
*/!*

function sayHi(name) {
  alert( `Hello, ${name}` );
}
```

Deklarasi Fungsi `sayHi` dibuat saat JavaScript siap memulai scriptnya dan terlihat di manapun di dalam.

...Jika ia Expresi Fungsi, maka ia tak akan berjalan:

```js run refresh untrusted
*!*
sayHi("John"); // galat!
*/!*

let sayHi = function(name) {  // (*) tak ada magic lagi
  alert( `Hello, ${name}` );
};
```

Expresi Fungsi dibuat saat exekusi mencapainya. Itu hanya akan terjadi pada baris `(*)`. Sudah telat banget.

Fitur spesial lain dari Deklarasi Fungsi ialah scope blok mereka.

**Di mode ketat, ketika Deklarasi Fungsi berada di jangkauan blok kode, ia terlihat di manapun di dalam blok. Tapi tidak di luarnya.**

Misalnya, bayangkan kita harus mendeklarasi fungsi `welcome()` tergantung variabel `age` yang kita dapat saat runtime. Lalu kita berencana akan menggunakannya kemudian.

Jika kita memakai Deklarasi Fungsi, ia tak akan bekerja sesuai harapan:

```js run
let age = prompt("What is your age?", 18);

// secara kondisional mendeklarasi fungsi
if (age < 18) {

  function welcome() {
    alert("Hello!");
  }

} else {

  function welcome() {
    alert("Greetings!");
  }

}

// ...pakai ini kemudian
*!*
welcome(); // Error: welcome is not defined
*/!*
```

Itu karena Deklarasi Fungsi cuma terlihat di dalam blok kode yang ia tinggali.

Ini contoh lainnya:

```js run
let age = 16; // ambil 16 sebagai contoh

if (age < 18) {
*!*
  welcome();               // \   (berjalan)
*/!*
                           //  |
  function welcome() {     //  |  
    alert("Hello!");       //  |  Deklarasi Fungsi tersedia
  }                        //  |  di manapun dalam blok kode tempat dia dideklarasi
                           //  |
*!*
  welcome();               // /   (berjalan)
*/!*

} else {

  function welcome() {    
    alert("Greetings!");
  }
}

// Di sini kita di luar kurung kurawal,
// jadi kita tak bisa melihat Deklarasi Fungsi yang dibuat di dalamnya.

*!*
welcome(); // Error: welcome is not defined
*/!*
```

Apa yang bisa kita lakukan supaya `welcome` terlihat di luar `if`?

Pendekatan yang benar ialah menggunakan Expresi Fungsi dan menetapkan `welcome` ke variabel yang dideklarasi di luar `if` dan punya visibilitas yang cukup.

Kode ini berjalan sesuai harapan:

```js run
let age = prompt("What is your age?", 18);

let welcome;

if (age < 18) {

  welcome = function() {
    alert("Hello!");
  };

} else {

  welcome = function() {
    alert("Greetings!");
  };

}

*!*
welcome(); // sekarang ok
*/!*
```

Atau kita bisa menyederhanakannya lebih lanjut menggunakan operator tanda tanya `?`:

```js run
let age = prompt("What is your age?", 18);

let welcome = (age < 18) ?
  function() { alert("Hello!"); } :
  function() { alert("Greetings!"); };

*!*
welcome(); // sekarang ok
*/!*
```


```smart header="Kapan harus memilih Deklarasi Fungsi versus Expresi Fungsi?"
Sebagai aturan praktis, saat kita harus mendeklarasi fungsi, hal pertama yang kita pertimbangkan ialah syntax Deklarasi Fungsi. Ia memberi kebebasan lebih dalam bagaimana mengorganisir kode kita, karena kita bisa memanggil fungsi macam ini sebelum mereka dideklarasi.

<<<<<<< HEAD
Itu juga untuk keterbacaan yang lebih baik, karena lebih mudah melihat `function f(…) {…}` dalam kode ketimbang `let f = function(…) {…}`. Deklarasi Fungsi lebih "eye-catching".
=======
That's also better for readability, as it's easier to look up `function f(…) {…}` in the code than `let f = function(…) {…};`. Function Declarations are more "eye-catching".
>>>>>>> ec21af8aef6930388c06ee4cd8f8f6769f9d305b

...Tapi jika Deklarasi Fungsi tak cocok untuk beberapa alasan, atau kita butuh deklarasi kondisional (kita sudah lihat contohnya), maka Expresi Fungsi sebaiknya digunakan.
```

<<<<<<< HEAD:1-js/02-first-steps/15-function-expressions-arrows/article.md

## Fungsi panah [#arrow-functions]

Ada lagi syntax yang lebih simpel dan ringkas untuk membuat fungsi, ia lebih baik dari Expresi Fungsi. Ia disebut "fungsi panah", karena rupanya seperti ini:


```js
let func = (arg1, arg2, ...argN) => expression
```

...Ini membuat fungsi `func` yang punya argumen `arg1..argN`, mengevaluasi `expression` di sisi kanan dengan penggunaan mereka dan mengembalikan hasilnya.

Dengan kata lain, kasarnya ia sama saja dengan:

```js
let func = function(arg1, arg2, ...argN) {
  return expression;
};
```

...Tapi lebih ringkas.

Ayo kita lihat contoh:

```js run
let sum = (a, b) => a + b;

/* Fungsi panah ialah bentuk pendek dari:

let sum = function(a, b) {
  return a + b;
};
*/

alert( sum(1, 2) ); // 3

```

Jika kita cuma punya satu argumen, maka tanda kurung sekitar parameter bisa dibuang, membuatnya lebih pendek lagi:

```js run
// sama dengan
// let double = function(n) { return n * 2 }
*!*
let double = n => n * 2;
*/!*

alert( double(3) ); // 6
```

Jika tak ada argumen, tanda kurung sebaiknya kosong (tapi mereka harus ada):

```js run
let sayHi = () => alert("Hello!");

sayHi();
```

Panah fungsi bisa digunakan dengan cara yang sama dengan Expresi Fungsi.

Misalnya, ini contoh yang ditulis ulang dengan `welcome()`:

```js run
let age = prompt("What is your age?", 18);

let welcome = (age < 18) ?
  () => alert('Hello') :
  () => alert("Greetings!");

welcome(); // ok now
```

Fungsi panah mungkin terlihat asing dan tak mudah terbaca pertama kali, tapi itu cepat berubah seiring mata memandangi strukturnya.

Mereka sangat nyaman untuk aksi sebaris simpel, saat kita malas banget untuk menulis banyak kata.

```smart header="Fungsi panah baris-ganda"

Contoh di atas mengambil argumen dari kiri `=>` dan mengealuasi expresi sisi kanan expression dengan mereka.

kadang kita butuh sesuatu yang lebih komplex, seperti pernyataan atau expresi ganda. Itu juga memungkinkan, tapi sebaiknya kita melampirkan mereka dalam kurung kurawal. Lalu gunakan `return` normal di dalamnya.

Seperti ini:

```js run
let sum = (a, b) => {  // kurung kurawal membuka fungsi baris-ganda
  let result = a + b;
*!*
  return result; // jika kita menggunakan kurung kurawal, gunakan return untuk mendapatkan hasil
*/!*
};

alert( sum(1, 2) ); // 3
```

```smart header="Ada lagi"
Di sini kita memuji fungsi panah karena keringkasan. Tapi belum selesai! Fungsi panah punya fitur menarik lain. Kita akan kembali ke mereka nanti di bab <info:arrow-functions>.

Untuk sekarang, kita sudah bisa menggunakan panah fungsi untuk aksi sebaris dan callback.
```

## Kesimpulan
=======
## Summary
>>>>>>> 2b5ac971c1bd8abe7b17cdcf724afd84799b6cbd:1-js/02-first-steps/15-function-expressions/article.md

- Fungsi adalah nilai. Mereka bisa diset, dikopi atau dideklarasi di kode manapun.
- Jika fungsi dideklarasi sebagai pernyataan terpisah di aliran kode utama, ia disebut "Deklarasi Fungsi".
- Jika fungsi dibuat sebagai bagian expresi, ia disebut "Expresi Fungsi".
- Deklarasi Fungsi diproses sebelum blok kode diexekusi. Mereka terlihat di manapun dalam blok.
- Expresi Fungsi dibuat saat aliran exekusi mencapai mereka.

Di banyak kasus saat kita harus mendeklarasi fungsi, Deklarasi Fungsi disenangi, karena ia terlihat sebelum deklarasi itu sendiri. Itu memberi kita flexibilitas lebih dalam organisasi kode, dan biasa lebih mudah terbaca.

<<<<<<< HEAD:1-js/02-first-steps/15-function-expressions-arrows/article.md
Jadi sebaiknya kita gunakan Expresi Fungsi hanya saat Deklarasi Fungsi tak cocok digunakan. Kita sudah melihat beberapa contoh itu di bab ini, dan kita akan melihat lebih lagi nanti.

Panah fungsi lebih praktis untuk satu baris. Mereka ada dalam dua rasa:

1. Tanpa kurung kurawal: `(...args) => expression` -- sisi kanan ialah expresi: fungsi mengevaluasinya dan mengembalikan hasil.
2. Dengan kurung kurawal: `(...args) => { body }` -- bracket memperbolehkan kita menulis pernyataan ganda di dalam fungsi, tapi kita butuh `return` explisit untuk mengembalikan sesuatu.
=======
So we should use a Function Expression only when a Function Declaration is not fit for the task. We've seen a couple of examples of that in this chapter, and will see more in the future.
>>>>>>> 2b5ac971c1bd8abe7b17cdcf724afd84799b6cbd:1-js/02-first-steps/15-function-expressions/article.md
