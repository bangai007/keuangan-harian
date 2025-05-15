<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Keuangan Harian</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; max-width: 480px; }
    h1 { text-align: center; }
    label { display: block; margin-top: 15px; }
    input[type="number"] { width: 100%; padding: 8px; margin-top: 5px; box-sizing: border-box; }
    button { margin-top: 20px; padding: 10px 15px; width: 100%; font-size: 16px; }
    .hasil { margin-top: 20px; font-weight: bold; color: green; }
    .riwayat { margin-top: 30px; }
    .riwayat-item { border-bottom: 1px solid #ccc; padding: 8px 0; }
  </style>
</head>
<body>
  <h1>Keuangan Harian</h1>

  <label for="saldoKemarin">Saldo Kemarin:</label>
  <input type="number" id="saldoKemarin" placeholder="Masukkan saldo kemarin" />

  <label for="belanja">Belanja Hari Ini:</label>
  <input type="number" id="belanja" placeholder="Masukkan jumlah belanja" />

  <label for="saldoSekarang">Saldo Sekarang:</label>
  <input type="number" id="saldoSekarang" placeholder="Masukkan saldo sekarang" />

  <button id="hitungBtn">Hitung & Simpan</button>

  <div class="hasil" id="hasilLaku"></div>

  <div class="riwayat">
    <h2>Riwayat Keuangan</h2>
    <div id="riwayatList"></div>
  </div>

  <script>
    const saldoKemarinInput = document.getElementById("saldoKemarin");
    const belanjaInput = document.getElementById("belanja");
    const saldoSekarangInput = document.getElementById("saldoSekarang");
    const hitungBtn = document.getElementById("hitungBtn");
    const hasilLakuDiv = document.getElementById("hasilLaku");
    const riwayatList = document.getElementById("riwayatList");

    function formatRupiah(num) {
      return "Rp " + num.toLocaleString("id-ID", { minimumFractionDigits: 0 });
    }

    function loadRiwayat() {
      const data = JSON.parse(localStorage.getItem("keuanganHarian") || "{}");
      const dates = Object.keys(data).sort((a, b) => b.localeCompare(a));
      riwayatList.innerHTML = "";

      if (dates.length === 0) {
        riwayatList.innerHTML = "<p>Belum ada data riwayat.</p>";
        return;
      }

      dates.forEach((tanggal) => {
        const item = data[tanggal];
        const div = document.createElement("div");
        div.className = "riwayat-item";
        div.textContent = `${tanggal} - Hasil Laku: ${formatRupiah(item.hasilLaku)}`;
        riwayatList.appendChild(div);
      });
    }

    function simpanData(tanggal, saldoKemarin, belanja, saldoSekarang, hasilLaku) {
      const data = JSON.parse(localStorage.getItem("keuanganHarian") || "{}");
      data[tanggal] = { saldoKemarin, belanja, saldoSekarang, hasilLaku };
      localStorage.setItem("keuanganHarian", JSON.stringify(data));
    }

    hitungBtn.addEventListener("click", () => {
      const sk = parseFloat(saldoKemarinInput.value);
      const bl = parseFloat(belanjaInput.value);
      const ss = parseFloat(saldoSekarangInput.value);

      if (isNaN(sk) || isNaN(bl) || isNaN(ss)) {
        alert("Mohon isi semua input dengan angka valid.");
        return;
      }

      const hasilLaku = ss + bl - sk;

      hasilLakuDiv.textContent = `Hasil Laku Hari Ini: ${formatRupiah(hasilLaku)}`;

      const tanggal = new Date().toISOString().slice(0, 10);
      simpanData(tanggal, sk, bl, ss, hasilLaku);

      loadRiwayat();

      saldoKemarinInput.value = "";
      belanjaInput.value = "";
      saldoSekarangInput.value = "";
    });

    // Load riwayat saat halaman dibuka
    loadRiwayat();
  </script>
</body>
</html>
