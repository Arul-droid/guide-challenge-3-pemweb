# Guide Pengerjaan Video Challenge Asistensi Modul 3

**Alur wajib:** Live Coding TypeScript Manual (Poin 1) → Vibe Coding dengan Harness (Poin 2–5).
**Wajib:** on cam sepanjang video, live coding (bukan hanya hasil akhir), upload ke YouTube.
**Nama video:** `NamaKelompok_Asistensi_Modul3_NamaAsisten`
**Form pengumpulan:** <https://forms.gle/qpKhndCBiz3BCx8P8>

---

## 0. Persiapan SEBELUM Rekam

### 0.1 Checklist

- [ ] Node.js + npm terpasang (`node -v`, `npm -v`)
- [ ] Git terpasang (`git --version`)
- [ ] **Dua harness** terpasang dan sudah login (lihat 0.2)
- [ ] Perekam layar + webcam (OBS / Zoom / GMeet) — layar penuh + wajah terlihat
- [ ] Nama kelompok, nama asisten, kelas sudah siap untuk intro
- [ ] Tutup notifikasi, tab pribadi, dan matikan Copilot/Gemini extension di editor (**dilarang AI di Poin 1**)

### 0.2 Pilih dua harness

Rekomendasi kombinasi (semua ada di modul):

| Peran | Pilihan | File konteks | Catatan |
| --- | --- | --- | --- |
| Harness A (Poin 2–5) | **OpenCode** | `AGENTS.md` | Ada `/init`, mode Plan (`Tab`), `/undo` |
| Harness B (Poin 5) | **Gemini CLI** / Codex CLI / Oh My Pi | `GEMINI.md` / `AGENTS.md` | Gemini CLI baca `GEMINI.md`, bukan `AGENTS.md` (kecuali diatur lewat `context.fileName`) |

Instalasi singkat:

```bash
# OpenCode
npm i -g opencode-ai@latest
# Gemini CLI
npm install -g @google/gemini-cli
# Codex CLI
npm install -g @openai/codex
```

> Pakai harness yang **file konteksnya `AGENTS.md`** untuk Poin 2–4 supaya sesuai instruksi tugas.

### 0.3 Siapkan proyek `task-tracker` (di luar rekaman)

Proyek ini harus sudah punya kode yang memakai tipe `Task`, supaya di Poin 4 compiler benar-benar menampilkan banyak error. Jalankan **sebelum** rekam:

```bash
mkdir task-tracker && cd task-tracker
git init
npm init -y
npm install typescript @types/node --save-dev
npx tsc --init
mkdir -p src/types data
npm pkg set scripts.typecheck="tsc --noEmit"
npm pkg set scripts.build="tsc"
npm pkg set scripts.start="node dist/index.js"
printf "node_modules\ndist\n.env\n" > .gitignore
```

Ganti `tsconfig.json` dengan versi dari modul (bagian 2):

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "rootDir": "src",
    "outDir": "dist",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "skipLibCheck": true,
    "types": ["node"]
  },
  "include": ["src"]
}
```

`src/types/task.ts`

```ts
export type TaskStatus = "todo" | "doing" | "done";

export interface Task {
  id: number;
  title: string;
  status: TaskStatus;
  dueDate?: string;
}

export type NewTask = Omit<Task, "id" | "status">;
```

`src/types/index.ts`

```ts
export * from "./task";
```

`src/tasks.ts`

```ts
import { NewTask, Task } from "./types/index";

export const tasks: Task[] = [
  { id: 1, title: "Belajar TypeScript", status: "done" },
  { id: 2, title: "Kerjakan Modul 3", status: "doing" },
  { id: 3, title: "Rekam video challenge", status: "todo" },
];

export function createTask(id: number, input: NewTask): Task {
  return { id, title: input.title, status: "todo", dueDate: input.dueDate };
}
```

`src/index.ts`

```ts
import { createTask, tasks } from "./tasks";

const [command] = process.argv.slice(2);

if (command === "list") {
  for (const t of tasks) {
    console.log(`#${t.id} [${t.status}] ${t.title}`);
  }
} else if (command === "demo") {
  console.log(createTask(99, { title: "Task contoh" }));
} else {
  console.log("Usage: list | demo");
}
```

Cek lalu **commit sebagai titik awal** (semua undo nanti kembali ke sini):

```bash
npm run typecheck      # harus 0 error
npm run build && npm start -- list
git add -A && git commit -m "chore: baseline task-tracker"
```

> Jika `tsc` mengeluh `Cannot find name 'process'/'console'`, pastikan `"types": ["node"]` ada di `tsconfig.json`.

### 0.4 Latihan Poin 1 di folder sementara

Latihan 2–3 kali supaya mengetik terlihat natural. Jalankan `npx tsx src/audit.ts` sekali dulu agar `tsx` ter-download (kalau tidak, di video muncul prompt install).

---

## Intro Video

**Aksi:** Wajah terlihat jelas.

**Ucapkan:**
> "Halo semuanya, perkenalkan nama saya Muhamad Nasrulhaq (Arul), NRP 5027251117, dari Kelas **[B/C]**, kelompok **[nama kelompok]**, dengan asisten **[nama asisten]**. Di video ini saya mengerjakan Challenge Asistensi Modul 3 secara berurutan, mulai dari live coding TypeScript manual sampai vibe coding dengan harness."

---

## Poin 1 — Audit Kode: Live Coding TypeScript Manual (TANPA AI)

> Syarat: kode 5–15 baris, pakai `any` **atau** `as` (bukan `as const`), tunjukkan bahayanya saat dijalankan, ganti dengan Union Type / Type Guard / Generics, lalu `npx tsc --noEmit` sampai 0 error, dan jelaskan alasannya.
> Boleh buka modul/internet/W3Schools. **Dilarang** AI atau extension AI.

### 1.1 Setup folder terpisah

```bash
mkdir audit-ts && cd audit-ts
npm init -y
npm install typescript @types/node --save-dev
npx tsc --init
mkdir src
```

Buka `tsconfig.json`, tunjukkan ke kamera bahwa `"strict": true` aktif (tidak dikomentari). Jika `console` tidak dikenali, tambahkan `"types": ["node"]`.

### 1.2 Tulis kode berbahaya — `src/audit.ts`

```ts
// Kode berbahaya: memakai 'as' pada data yang sebenarnya angka
const dataDariAPI: unknown = 123;
const pesan = (dataDariAPI as string).toUpperCase();
console.log(pesan);
```

**Ucapkan:** "Kode ini tidak error di TypeScript, karena `as` memaksa compiler percaya bahwa data ini string."

### 1.3 Jalankan dan tunjukkan error runtime

```bash
npx tsx src/audit.ts
```

Hasil yang diharapkan: `TypeError: dataDariAPI.toUpperCase is not a function`.

**Ucapkan:** "Nilai aslinya number, bukan string. Type assertion `as` mematikan pengecekan tipe, sehingga bug baru ketahuan saat program berjalan."

### 1.4 Perbaiki dengan Union Type + Type Guard

> ⚠️ **Jangan** tulis `const x: string | number = 123;` lalu `if (typeof x === "string")` di level atas. TypeScript mempersempit `x` menjadi `number` karena nilai awalnya, sehingga di dalam `if` tipenya `never` dan `x.toUpperCase()` malah error.
> Solusi: bungkus dalam fungsi dengan parameter `string | number`.

Ubah `src/audit.ts` menjadi:

```ts
// Perbaikan: Union Type + Type Guard
function prosesData(data: string | number): void {
  if (typeof data === "string") {
    console.log(data.toUpperCase());
  } else {
    console.log("Data bukan string, ini angka:", data);
  }
}

prosesData(123);
prosesData("halo");
```

### 1.5 Verifikasi

```bash
npx tsc --noEmit        # tidak boleh ada output error
npx tsx src/audit.ts    # tunjukkan kedua cabang jalan
```

**Ucapkan (penjelasan perubahan):** "Saya mengganti `unknown` + `as` dengan union type `string | number` dan type guard `typeof`. Sekarang compiler tahu di cabang `if` datanya pasti string, dan di `else` pasti number. Pengecekan terjadi sebelum memanggil `toUpperCase`, jadi tidak ada crash saat runtime."

---

## Poin 2 — Instalasi & Pengenalan Harness

**Syarat:** instal & kenali satu harness, jalankan di `task-tracker`, minta jelaskan struktur proyek, catat file instruksi yang dibaca.

```bash
cd ../task-tracker
opencode            # atau harness pilihanmu
```

**Ucapkan:** "Saya memakai harness **[nama]**, dijalankan di folder task-tracker."

**Prompt:**

```text
Tolong jelaskan struktur proyek task-tracker ini secara singkat.
```

**Yang dicatat/ditunjukkan:** nama file instruksi yang dibaca harness (kalau belum ada, sebutkan file yang *akan* dibaca: `AGENTS.md` untuk OpenCode/Codex/Oh My Pi, `GEMINI.md` untuk Gemini CLI, `CLAUDE.md` untuk Claude Code).

**Ucapkan:** "Harness menjelaskan struktur folder. File instruksi yang dibaca harness ini adalah **[nama file]**."

---

## Poin 3 — Konteks dengan `AGENTS.md`

**Syarat:** buat `AGENTS.md` (atau `/init`), bandingkan hasil permintaan yang **sama** dengan dan tanpa file itu, jelaskan perbedaannya.

Pastikan **belum ada** `AGENTS.md` (`ls`).

### 3.1 Tanpa AGENTS.md

**Prompt** (dipilih karena data dari JSON sering memancing `as`/`any`):

```text
Buat fitur add: `npm start -- add "judul"` yang menambah task baru
dan menyimpannya ke data/tasks.json (baca file lama jika ada).
```

Biarkan selesai, lalu cek:

```bash
npm run typecheck
grep -rnE "\bany\b|@ts-ignore| as " src/
git status
```

**Ucapkan:** hasil tanpa konteks, misalnya "agent memakai `as Task[]` / `any` pada hasil `JSON.parse`, tipe ditaruh di file sembarang."

### 3.2 Kembalikan ke kondisi awal

```bash
git restore .
git clean -fd
```

(Atau `/undo` di OpenCode.) Pastikan `git status` bersih supaya perbandingan adil.

### 3.3 Buat `AGENTS.md`

Keluar dari harness (atau buka terminal baru) dan buat `AGENTS.md` di root:

````markdown
# AGENTS.md

## Proyek
Task Tracker CLI berbasis Node.js + TypeScript. Data disimpan di `data/tasks.json`.

## Perintah
- Typecheck: `npm run typecheck`
- Build: `npm run build`
- Jalankan: `npm start -- <perintah>`

## Aturan kode
- TypeScript `strict`. **Dilarang** memakai `any`, `@ts-ignore`, atau `as`.
- Semua tipe ada di `src/types/` dan diekspor lewat `index.ts`.
- Fungsi publik wajib punya type annotation untuk parameter dan return.
- Data dari luar (argumen CLI, isi JSON) bertipe `unknown` lalu divalidasi.
- Jangan menambah dependency baru tanpa meminta persetujuan.

## Alur kerja
1. Jelaskan rencana singkat sebelum mengedit.
2. Setelah mengedit, jalankan `npm run typecheck` dan perbaiki semua error.
3. Jangan mengubah file di luar yang dibutuhkan tugas.
````

> Alternatif: jalankan `/init` di harness lalu edit hasilnya. Jika memakai Gemini CLI, namanya `GEMINI.md`.

### 3.4 Dengan AGENTS.md

Masuk harness lagi, **prompt yang sama persis**:

```text
Baca AGENTS.md. Lalu buat fitur add: `npm start -- add "judul"` yang menambah task baru
dan menyimpannya ke data/tasks.json (baca file lama jika ada).
```

(Kata "Baca AGENTS.md" boleh ditambahkan sebagai satu-satunya perbedaan prompt, seperti di script asisten.)

Cek lagi:

```bash
npm run typecheck
grep -rnE "\bany\b|@ts-ignore| as " src/
```

**Ucapkan (perbedaan):** "Tanpa AGENTS.md agent [pakai `as`/`any`, tidak validasi, tidak menjalankan typecheck]. Dengan AGENTS.md agent [mengikuti `src/types/`, memakai `unknown` lalu validasi, tanpa `any`/`as`, menjalankan typecheck sendiri]. Konteks proyek membuat hasil lebih searah dengan aturan kita."

Lalu simpan hasilnya sebagai titik awal Poin 4:

```bash
git add -A && git commit -m "feat: add command with AGENTS.md rules"
```

---

## Poin 4 — Fitur Baru: Priority

**Syarat:** tambah field `priority: "low" | "medium" | "high"` ke `Task`, biarkan compiler menunjukkan semua error, minta agent memperbaiki, tunjukkan prosesnya.

### 4.1 Edit manual (di luar harness)

Buka `src/types/task.ts`, tambahkan:

```ts
export interface Task {
  id: number;
  title: string;
  status: TaskStatus;
  priority: "low" | "medium" | "high"; // <-- baris baru
  dueDate?: string;
}
```

### 4.2 Biarkan compiler menunjukkan error

```bash
npm run typecheck
```

**Ucapkan:** "Compiler langsung menunjukkan semua tempat yang belum punya `priority`: data awal di `tasks.ts`, `createTask`, dan kode fitur `add`."

### 4.3 Minta agent memperbaiki

**Prompt:**

```text
Saya baru saja menambahkan field "priority": "low" | "medium" | "high" pada Task.
Tolong perbaiki seluruh error TypeScript di proyek ini yang disebabkan oleh perubahan tersebut.
Jangan pakai any atau as.
```

Tunjukkan proses agent (file yang diedit, `typecheck` yang dijalankan).

### 4.4 Verifikasi

```bash
npm run typecheck     # 0 error
git diff              # tunjukkan perubahan
npm run build && npm start -- list
```

**Ucapkan:** "Compiler yang menemukan semua titik yang terdampak, agent tinggal memperbaikinya. Sekarang `tsc` bersih."

```bash
git add -A && git commit -m "feat: add priority to Task"
```

---

## Poin 5 — Perbandingan Dua Harness

**Syarat:** fitur yang sama di dua harness: `list --status done`. Bandingkan kualitas rencana, jumlah error `tsc`, banyaknya intervensi manual, kenyamanan alur kerja, lalu kesimpulan **1 paragraf**.

**Prompt (identik untuk keduanya):**

```text
Buat fitur baru yaitu list --status done untuk menampilkan task yang sudah selesai.
Buat rencananya dulu, baru implementasikan.
```

### 5.1 Harness A

1. Pastikan `git status` bersih.
2. Jalankan prompt di atas.
3. Catat: **rencana** (terstruktur atau tidak), **jumlah error `tsc`**, **berapa kali kamu harus turun tangan**, kenyamanan.

```bash
npm run typecheck
npm run build && npm start -- list --status done
git diff --stat
```

4. Buang hasilnya:

```bash
git restore .
git clean -fd
```

### 5.2 Harness B

1. Buka harness kedua di folder yang sama, `git status` bersih.
2. Jalankan **prompt yang persis sama**.
3. Catat 4 hal yang sama dan jalankan perintah verifikasi yang sama.

### 5.3 Tabel catatan (isi saat uji, boleh ditampilkan di layar)

| Kriteria | Harness A | Harness B |
| --- | --- | --- |
| Kualitas rencana | | |
| Jumlah error `tsc` | | |
| Intervensi manual | | |
| Kenyamanan alur kerja | | |

### 5.4 Kesimpulan 1 paragraf (ucapkan, isi sesuai hasil nyata!)

> "Dari perbandingan ini, kualitas rencana **[Harness A]** terasa **[lebih/kurang]** terstruktur dibanding **[Harness B]** karena **[alasan]**. Saat dicek dengan `tsc`, **[Harness A]** menghasilkan **[n]** error sedangkan **[Harness B]** menghasilkan **[n]** error, yaitu **[penyebab]**. Intervensi manual yang saya lakukan: **[A: n kali]** dan **[B: n kali]**. Secara keseluruhan, kenyamanan alur kerja lebih terasa pada **[harness mana]** karena **[alasan]**."

> ⚠️ Jangan membaca template apa adanya. Jumlah error dan penyebab harus sesuai yang benar-benar terjadi di videomu.

---

## Penutup Video

> "Demikian pengerjaan Video Challenge Asistensi Modul 3 dari saya. Terima kasih."

---

## Troubleshooting Cepat

| Masalah | Solusi |
| --- | --- |
| `Cannot find name 'console'/'process'` | Tambahkan `"types": ["node"]` di `tsconfig.json`, pastikan `@types/node` terpasang |
| `npx tsx` minta install saat rekaman | Jalankan sekali sebelum rekam |
| `x.toUpperCase()` error `never` di Poin 1 | Pakai fungsi dengan parameter `string \| number` (lihat 1.4) |
| Agent mengubah banyak file tak terduga | `git restore .` dan `git clean -fd`, ulangi dengan prompt lebih spesifik |
| Gemini CLI tidak membaca `AGENTS.md` | Pakai `GEMINI.md`, atau set `context.fileName` di `~/.gemini/settings.json` |
| Hasil Poin 3 tanpa/dengan AGENTS.md terlihat sama | Cek `grep` untuk `any`/`as`, pakai prompt yang melibatkan data eksternal (JSON/argv) |
| Rencana harness tidak muncul | Minta eksplisit: "Jangan edit apa pun dulu, buat rencana saja" (OpenCode: `Tab` ke agent Plan) |

---

## Checklist Sebelum Submit

- [ ] Poin 1 dikerjakan manual dulu, tanpa AI, `tsc --noEmit` 0 error
- [ ] Poin 2–5 dilakukan berurutan dengan harness
- [ ] Wajah terlihat sepanjang video
- [ ] Video live coding (proses terlihat, bukan hanya hasil)
- [ ] Video di-upload ke YouTube dan linknya bisa diakses
- [ ] Judul video: `NamaKelompok_Asistensi_Modul3_NamaAsisten`
- [ ] Data di Google Form benar: <https://forms.gle/qpKhndCBiz3BCx8P8>
