---
title: "Tips Klik White Space Pada Anak Widget Gesturedetector Berupa Column Atau Row"
date: 2022-12-10T02:01:41+07:00
draft: false
---

Mengembangkan aplikasi yang memiliki antarmuka yang jelas dan bersih akan memudahkan pengguna dalam memakai aplikasi. *White space* atau spasi antar komponen menjadi salah satu hal yang penting dalam membuat antarmuka yang jelas dan bersih. Walaupun *white space* tidak terlihat, *white space* juga dapat menyulitkan.


Saya pernah implementasi desain menu menjadi widget Flutter. Ekspektasi yang diharapkan, aplikasi menuju ke halaman lain saat menu disentuh. Menu berfungsi dengan baik ketika disentuh pada bagian tulisan atau *icon*. Namun, tidak berfungsi pada bagian *white space*.


## Contoh Kasus
Mari kita coba mereplikasi kasus tersebut dengan mengimplementasikan menu *account* pada desain. Terlihat ada *white space* yang lebar di antara *icon* dan label *Account*.
### Desain
![Menu Account](menu-account.png)

### Code
Implementasi desain di atas seperti pada kode di bawah ini.

```dart
GestureDetector(
	onTap: () {
		ScaffoldMessenger.of(context).showSnackBar(
			const SnackBar(content: Text('Account')),
		);
	},
	child: Row(
		mainAxisAlignment: MainAxisAlignment.spaceBetween,
		children: [
			const Padding(
				padding: EdgeInsets.all(8),
				child: Icon(Icons.person),
			),
			Text(
				'Account',
				style: Theme.of(context).textTheme.bodyLarge,
			),
		],
	),
)
```


### Hasil
{{< video src="defect.mov" type="video/mp4" preload="auto" >}}

## Solusi

Terlihat pada video di atas, kita dapat mereplikasi kasus di atas. Solusinya adalah dengan mengatur *behavior* *widget* **GestureDetector** dengan `HitTestBehavior.translucent` atau `HitTestBehavior.opaque`.

### Code
```dart
GestureDetector(
	behavior: HitTestBehavior.translucent, // or HitTestBehavior.opaque
```

### Hasil
Hasilnya pada video ini, *white space* menu *account* dapat memberikan response saat disentuh.

{{< video src="fixed.mov" type="video/mp4" preload="auto" >}}

## Penjelasan
Kasus di atas terjadi karena secara bawaan properti `behavior` pada `GestureDetector` bernilai `HitTestBehavior.deferToChild` saat properti *child*-nya terisi. Sebelum mengenal `HitTestBehavior.deferToChild`, kita harus tahu apa itu `HitTestBehavior`.


`HitTestBehavior` pada `GestureDetector` menentukan bagaimana cara meresponse *gesture* selama *hit testing*. *Hit testing* merupakan proses menentukan elemen antarmuka pengguna yang ada di bawah titik sentuh. 


Kembali ke `HitTestBehavior.deferToChild`. `HitTestBehavior.deferToChild` merupakan nilai untuk mengatur `GestureDetector` agar menunda *hit testing* hingga widget anak. Artinya anak widget-nya yang menentukan apakah harus merespons *hit test* atau tidak. Oleh karena itu, *white spac*e pada menu *Account* tidak meresponse saat disentuh.


Seperti yang sudah saya jelaskan, ada 2 solusi nilai `HitTestBehavior` untuk memperbaiki kasus di atas. Pertama, `HitTestBehavior.opaque` merupakan nilai untuk mengatur `GestureDetector` agar benar-benar tembus terhadap *hit testing*. Artinya *hit test* akan selalu berhasil walaupun ada widget anak yang tidak merespon *hit test*.


Terakhir, `HitTestBehavior.translucent` merupakan nilai untuk mengatur `GestureDetector` agar sebagian transparan terhadap *hit testing*. Artinya setiap *hit test* akan selalu berhasil jika widget anak merespons *hit test*.


Demikian tips klik *white space* pada anak widget `GestureDetector` berupa `Column` atau `Row`.