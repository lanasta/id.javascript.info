# Rekursi dan stack

Mari kita kembali ke fungsi dan mempelajarinya lebih dalam.

Topik pertama kita adalah *recursion*(pengulangan).

Jika kamu tidak baru dalam hal programing, kamu mungkin sudah akrab dengan rekursi dan bisa meloncati bab ini.

Rekursi adalah pola programing yang berguna di dalam situasi-situasi dimana sesuatu tugas bisa dengan alami dibagikan ke beberapa tugas-tugas yang sama jenisnya, tetapi lebih sederhana. Atau kapan sesuatu tugas bisa disederhanakan menjadi aksi yang gampang ditambah varian lebih biasa dari tugas yang sama. Atau, dan kita akan lihat segera, untuk menangani struktur data tertentu.

Ketika sebuah fungsi menyelesaikan suatu tugas, di dalam prosesnya ia bisa memanggil banyak fungsi-fungsi yang lain. Hal parsial dari ini adalah ketika sesuatu fungsi memanggil *dirinya sendiri*. Itu yang disebut *rekursi*.

## Dua cara berfikir

Bagi sesuatu yang sederhana untuk memulai -- mari kita tulis fungsi `pow(x, n` yang memangkatkan `x` ke nilai `n`. Dalam kata lain, mengalikan `x` dengan dirinya sendiri `n` kali.

```js
pow(2, 2) = 4
pow(2, 3) = 8
pow(2, 4) = 16
```

Ada dua cara untuk mengimplementasinya.

1. Pemikiran iteratif: loop `for`:

    ```js run
    function pow(x, n) {
      let result = 1;

      // kalikan hasil x n kali di dalam loop
      for (let i = 0; i < n; i++) {
        result *= x;
      }

      return result;
    }

    alert( pow(2, 3) ); // 8
    ```

2. Pemikiran rekursif: sederhanakan tugas dan panggilah diri sendiri:

    ```js run
    function pow(x, n) {
      if (n == 1) {
        return x;
      } else {
        return x * pow(x, n - 1);
      }
    }

    alert( pow(2, 3) ); // 8
    ```

Perhatikan bagaimana varian rekursifnya berbeda secara fundamental.

Ketika `pow(x, n)` dipanggil, eksekusinya terbagi ke dua cabang:

```js
              if n==1  = x
             /
pow(x, n) =
             \       
              else     = x * pow(x, n - 1)
```

1. Jika `n == 1`, maka semuanya menjadi gampang. Ini dipanggil *dasar* rekursi, karena ia dengan cepat menghasilkan nilai yang jelas: `pow(x, 1)` sama dengan `x`. 
2. Jika tidak, kita bisa merepresentasikan `pow(x, n)` sebagai `x * pow(x, n - 1)`. Di matematika, orang menulisnya <code>x<sup>n</sup> = x * x<sup>n-1</sup></code>. Ini disebut *langkah rekursif*: kita merubah tugasnya menjadi aksi yang lebih sederhana (`pow` dengan nilai `n` lebih rendah). Langkah-langkah selanjutnya menyederhanakannya lebih lagi sampai `n` menjadi `1`.

Kita juga bisa bilang bahwa `pow` *memanggil dirinya sendiri secara rekursif* sampai `n == 1`.

![diagram rekursif pow](recursion-pow.svg)


Sebagai contoh, untuk menghitung `pow(2, 4)` varian rekursifnya mengikuti langkah-langkah ini:

1. `pow(2, 4) = 2 * pow(2, 3)`
2. `pow(2, 3) = 2 * pow(2, 2)`
3. `pow(2, 2) = 2 * pow(2, 1)`
4. `pow(2, 1) = 2`

Jadi, rekursinya mengurangi suatu panggilan fungsi menjadi lebih sederhana, lalu -- lebih dan lebih sederhana lagi, dan sebagainya, sampai hasilnya menjadi jelas.

````smart header="Rekursi biasanya lebih pendek"
Solusi rekursif biasanya lebih pendek dari pada yang iteratif.

Disini kita bisa menulis ulang yang sama dengan operator kondisional `?` melainkan `if` untuk membuat `pow(x, n)` lebih pendek dan masih gampang dibaca:

```js run
function pow(x, n) {
  return (n == 1) ? x : (x * pow(x, n - 1));
}
```
````

Hitungan maksimum panggilan berlapis (termasuk yang pertama) disebut *kedalaman rekursi*. Dalam contoh kita, tepatnya `n`.

Kedalaman maksimum rekursi dibatasi dengan engine JavaScript. Kita bisa mengandalkan 1000, beberapa engine bisa memungkinkan lebih, tapi 100000 biasanya kemungkinan besar di luar batas. Ada optimisiasi otomatis yang bisa meringankan ini ("tail calls optimizations"), tetapi belum di dukung dimana-mana dan hanya berfungsi untuk hal yang sederhana.

Hal itu membatasi aplikasi rekursi, tetapi aplikasinya masih tetap sangat luas. Ada banyak tugas dimana pemikiran rekursif menghasilkan kode yang lebih sederhana, yang lebih gampang dipelihara.

## Eksekusi konteks dan stack

Sekarang mari kita periksa bagaimana panggilan rekursif bekerja. Untuk itu kita akan melihat dibawah tudung fungsi-fungsi.

Informasi tentang proses eksekusi suatu fungsi disimpan di dalam *konteks eksekusi*-nya.

[Eksekusi konteks](https://tc39.github.io/ecma262/#sec-execution-contexts) adalah struktur data internal yang berisi detail tentang eksekusi sebuah fungsi: di mana aliran kontrolnya, variabelnya, nilai dari `this` (kita tidak memakainya disini) dan beberapa detail intern yang lain.

Satu panggilan fungsi mempunyai tepat satu konteks eksekusi terkait dengannya.

Ketika sebuah fungsi membuat panggilan nested, hal ini terjadi:

- Fungsi saat ini dijedakan.
- Konteks eksekusi yang terkait dengannya akan disimpan dalam struktur data spesial yang disebut *stack konteks eksekusi*.
- Panggilan nested dijalankan
- Setelah berakhir, konteks eksekusi yang lama diambil dari stack, dan fungsi luar dilanjutkan dari tempat ia berhenti.

Mari kita lihat apa yang terjadi di dalam panggilan `pow(2, 3)`.

### pow(2, 3)

Di awal panggilan `pow(2, 3)` konteks eksekusinya akan menyimpan variabel: `x = 2, n = 3`, aliran eksekusinya berada di baris `1` daripada fungsinya.

Kita bisa menggambarkanya sebagai:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 3, di baris 1 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Itu lokasi dimana fungsinya akan mulai tereksekusi. Kondisi `n == 1` itu salah, jadi alirannya berlanjut ke cabang kedua daripada `if`:

```js run
function pow(x, n) {
  if (n == 1) {
    return x;
  } else {
*!*
    return x * pow(x, n - 1);
*/!*
  }
}

alert( pow(2, 3) );
```

Variabelnya sama, tapi barisnya berganti, jadi konteksnya sekarang adalah:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 3, di baris 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Untuk menghitung `x * pow(x, n - 1)`, kita perlu membuat sub-panggilan `pow` dengan argumen baru `pow(2, 2)`.

### pow(2, 2)

Untuk membuat panggilan nested, JavaScript mengingat konteks eksekusi sekarang ini di dalam *stack konteks eksekusi*.

Disini kita memanggil fungsi yang sama `pow`, tetapi itu tidak berpengaruh. Prosesnya sama bagi semua fungsi:

1. Konteks sekarang "diingat" di atas stack atau tumpukan.
2. Konteks barunya diciptakan untuk sub-panggilan.
3. Ketika sub-panggilan selesai -- konteks sebelumnya akan di pop atau di angkat keluar dari stack, dan eksekusinya berlanjut.

Ini konteks stack-nya ketika kita memasuki sub-panggilan `pow(2, 2)`:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 2, di baris 1 }</span>
    <span class="function-execution-context-call">pow(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 3, di baris 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Konteks eksekusi barunya ada diatas (dan bold), dan konteks-konteks sebelumnya ada dibawah.

Ketika kita menyelesaikan subcallnya -- mudah untuk melanjutkan konteks sebelumnya, karena ia menyimpan kedua variabelnya dan lokasi kode dimana ia berhenti.

```smart
Di dalam gambar ini kita menggunakan kata "line" (baris), seperti di contoh hanya ada satu subcall di baris, tetapi umumnya satu baris kode bisa mengandung beberapa subcall, seperti `pow(…) + pow(…) + yangLain(…)`.

Jadi akan lebih akurat untuk mengatakan bahwa eksekusinya berlanjut "langsung setelah subcall".
```

### pow(2, 1)

Prosesnya berulang: subcall baru dipanggil di baris `5`, sekarang dengan argumen `x=2`, `n=1`.

Konteks eksekusi baru diciptakan, yang sebelumnya didorong ke atas stack (tumpukan):

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 1, di baris 1 }</span>
    <span class="function-execution-context-call">pow(2, 1)</span>
  </li>
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 2, di baris 5 }</span>
    <span class="function-execution-context-call">pow(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 3, di baris 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

Ada 2 konteks lama sekarang dan 1 yang sedang berjalan untuk `pow(2, 1)`.

### The exit (proses keluar)

Saat `pow(2, 1)` dieksekusi, berbeda dari sebelumnya, kondisi `n == 1` benar, jadi cabang pertama dari `if` berlaku:

```js
function pow(x, n) {
  if (n == 1) {
*!*
    return x;
*/!*
  } else {
    return x * pow(x, n - 1);
  }
}
```

Tidak ada lagi panggilan nested, jadi fungsinya selesai, mengembalikan `2`.

Saat fungsinya selesai, konteks eksekusinya tidak diperlukan lagi, jadi ia dikeluarkan dari memori. Yang sebelumnya dikembalikan ke atas stack:


<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 2, di baris 5 }</span>
    <span class="function-execution-context-call">pow(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Konteks: { x: 2, n: 3, di baris 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

The execution of `pow(2, 2)` is resumed. It has the result of the subcall `pow(2, 1)`, so it also can finish the evaluation of `x * pow(x, n - 1)`, returning `4`.

Then the previous context is restored:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Context: { x: 2, n: 3, at line 5 }</span>
    <span class="function-execution-context-call">pow(2, 3)</span>
  </li>
</ul>

When it finishes, we have a result of `pow(2, 3) = 8`.

The recursion depth in this case was: **3**.

As we can see from the illustrations above, recursion depth equals the maximal number of context in the stack.

Note the memory requirements. Contexts take memory. In our case, raising to the power of `n` actually requires the memory for `n` contexts, for all lower values of `n`.

A loop-based algorithm is more memory-saving:

```js
function pow(x, n) {
  let result = 1;

  for (let i = 0; i < n; i++) {
    result *= x;
  }

  return result;
}
```

The iterative `pow` uses a single context changing `i` and `result` in the process. Its memory requirements are small, fixed and do not depend on `n`.

**Any recursion can be rewritten as a loop. The loop variant usually can be made more effective.**

...But sometimes the rewrite is non-trivial, especially when function uses different recursive subcalls depending on conditions and merges their results or when the branching is more intricate. And the optimization may be unneeded and totally not worth the efforts.

Recursion can give a shorter code, easier to understand and support. Optimizations are not required in every place, mostly we need a good code, that's why it's used.

## Recursive traversals

Another great application of the recursion is a recursive traversal.

Imagine, we have a company. The staff structure can be presented as an object:

```js
let company = {
  sales: [{
    name: 'John',
    salary: 1000
  }, {
    name: 'Alice',
    salary: 600
  }],

  development: {
    sites: [{
      name: 'Peter',
      salary: 2000
    }, {
      name: 'Alex',
      salary: 1800
    }],

    internals: [{
      name: 'Jack',
      salary: 1300
    }]
  }
};
```

In other words, a company has departments.

- A department may have an array of staff. For instance, `sales` department has 2 employees: John and Alice.
- Or a department may split into subdepartments, like `development` has two branches: `sites` and `internals`. Each of them has their own staff.
- It is also possible that when a subdepartment grows, it divides into subsubdepartments (or teams).

    For instance, the `sites` department in the future may be split into teams for `siteA` and `siteB`. And they, potentially, can split even more. That's not on the picture, just something to have in mind.

Now let's say we want a function to get the sum of all salaries. How can we do that?

An iterative approach is not easy, because the structure is not simple. The first idea may be to make a `for` loop over `company` with nested subloop over 1st level departments. But then we need more nested subloops to iterate over the staff in 2nd level departments like `sites`... And then another subloop inside those for 3rd level departments that might appear in the future? If we put 3-4 nested subloops in the code to traverse a single object, it becomes rather ugly.

Let's try recursion.

As we can see, when our function gets a department to sum, there are two possible cases:

1. Either it's a "simple" department with an *array* of people -- then we can sum the salaries in a simple loop.
2. Or it's *an object* with `N` subdepartments -- then we can make `N` recursive calls to get the sum for each of the subdeps and combine the results.

The 1st case is the base of recursion, the trivial case, when we get an array.

The 2nd case when we get an object is the recursive step. A complex task is split into subtasks for smaller departments. They may in turn split again, but sooner or later the split will finish at (1).

The algorithm is probably even easier to read from the code:


```js run
let company = { // the same object, compressed for brevity
  sales: [{name: 'John', salary: 1000}, {name: 'Alice', salary: 600 }],
  development: {
    sites: [{name: 'Peter', salary: 2000}, {name: 'Alex', salary: 1800 }],
    internals: [{name: 'Jack', salary: 1300}]
  }
};

// The function to do the job
*!*
function sumSalaries(department) {
  if (Array.isArray(department)) { // case (1)
    return department.reduce((prev, current) => prev + current.salary, 0); // sum the array
  } else { // case (2)
    let sum = 0;
    for (let subdep of Object.values(department)) {
      sum += sumSalaries(subdep); // recursively call for subdepartments, sum the results
    }
    return sum;
  }
}
*/!*

alert(sumSalaries(company)); // 6700
```

The code is short and easy to understand (hopefully?). That's the power of recursion. It also works for any level of subdepartment nesting.

Here's the diagram of calls:

![recursive salaries](recursive-salaries.svg)

We can easily see the principle: for an object `{...}` subcalls are made, while arrays `[...]` are the "leaves" of the recursion tree, they give immediate result.

Note that the code uses smart features that we've covered before:

- Method `arr.reduce` explained in the chapter <info:array-methods> to get the sum of the array.
- Loop `for(val of Object.values(obj))` to iterate over object values: `Object.values` returns an array of them.


## Recursive structures

A recursive (recursively-defined) data structure is a structure that replicates itself in parts.

We've just seen it in the example of a company structure above.

A company *department* is:
- Either an array of people.
- Or an object with *departments*.

For web-developers there are much better-known examples: HTML and XML documents.

In the HTML document, an *HTML-tag* may contain a list of:
- Text pieces.
- HTML-comments.
- Other *HTML-tags* (that in turn may contain text pieces/comments or other tags etc).

That's once again a recursive definition.

For better understanding, we'll cover one more recursive structure named "Linked list" that might be a better alternative for arrays in some cases.

### Linked list

Imagine, we want to store an ordered list of objects.

The natural choice would be an array:

```js
let arr = [obj1, obj2, obj3];
```

...But there's a problem with arrays. The "delete element" and "insert element" operations are expensive. For instance, `arr.unshift(obj)` operation has to renumber all elements to make room for a new `obj`, and if the array is big, it takes time. Same with `arr.shift()`.

The only structural modifications that do not require mass-renumbering are those that operate with the end of array: `arr.push/pop`. So an array can be quite slow for big queues, when we have to work with the beginning.

Alternatively, if we really need fast insertion/deletion, we can choose another data structure called a [linked list](https://en.wikipedia.org/wiki/Linked_list).

The *linked list element* is recursively defined as an object with:
- `value`.
- `next` property referencing the next *linked list element* or `null` if that's the end.

For instance:

```js
let list = {
  value: 1,
  next: {
    value: 2,
    next: {
      value: 3,
      next: {
        value: 4,
        next: null
      }
    }
  }
};
```

Graphical representation of the list:

![linked list](linked-list.svg)

An alternative code for creation:

```js no-beautify
let list = { value: 1 };
list.next = { value: 2 };
list.next.next = { value: 3 };
list.next.next.next = { value: 4 };
list.next.next.next.next = null;
```

Here we can even more clearer see that there are multiple objects, each one has the `value` and `next` pointing to the neighbour. The `list` variable is the first object in the chain, so following `next` pointers from it we can reach any element.

The list can be easily split into multiple parts and later joined back:

```js
let secondList = list.next.next;
list.next.next = null;
```

![linked list split](linked-list-split.svg)

To join:

```js
list.next.next = secondList;
```

And surely we can insert or remove items in any place.

For instance, to prepend a new value, we need to update the head of the list:

```js
let list = { value: 1 };
list.next = { value: 2 };
list.next.next = { value: 3 };
list.next.next.next = { value: 4 };

*!*
// prepend the new value to the list
list = { value: "new item", next: list };
*/!*
```

![linked list](linked-list-0.svg)

To remove a value from the middle, change `next` of the previous one:

```js
list.next = list.next.next;
```

![linked list](linked-list-remove-1.svg)

We made `list.next` jump over `1` to value `2`. The value `1` is now excluded from the chain. If it's not stored anywhere else, it will be automatically removed from the memory.

Unlike arrays, there's no mass-renumbering, we can easily rearrange elements.

Naturally, lists are not always better than arrays. Otherwise everyone would use only lists.

The main drawback is that we can't easily access an element by its number. In an array that's easy: `arr[n]` is a direct reference. But in the list we need to start from the first item and go `next` `N` times to get the Nth element.

...But we don't always need such operations. For instance, when we need a queue or even a [deque](https://en.wikipedia.org/wiki/Double-ended_queue) -- the ordered structure that must allow very fast adding/removing elements from both ends, but access to its middle is not needed.

Lists can be enhanced:
- We can add property `prev` in addition to `next` to reference the previous element, to move back easily.
- We can also add a variable named `tail` referencing the last element of the list (and update it when adding/removing elements from the end).
- ...The data structure may vary according to our needs.

## Summary

Terms:
- *Recursion*  is a programming term that means calling a function from itself. Recursive functions can be used to solve tasks in elegant ways.

    When a function calls itself, that's called a *recursion step*. The *basis* of recursion is function arguments that make the task so simple that the function does not make further calls.

- A [recursively-defined](https://en.wikipedia.org/wiki/Recursive_data_type) data structure is a data structure that can be defined using itself.

    For instance, the linked list can be defined as a data structure consisting of an object referencing a list (or null).

    ```js
    list = { value, next -> list }
    ```

    Trees like HTML elements tree or the department tree from this chapter are also naturally recursive: they branch and every branch can have other branches.

    Recursive functions can be used to walk them as we've seen in the `sumSalary` example.

Any recursive function can be rewritten into an iterative one. And that's sometimes required to optimize stuff. But for many tasks a recursive solution is fast enough and easier to write and support.
