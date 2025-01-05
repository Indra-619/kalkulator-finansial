<template>
  <div class="container">
    <!-- Title Kelompok Angin -->


    <form @submit.prevent="calculate" class="form">
      <div class="form-group">
        <label for="penjualan">Penjualan (Rp) - Pisahkan dengan koma untuk beberapa nilai:</label>
        <input type="text" v-model="penjualanInput" id="penjualan" required placeholder="Contoh: 1000000,2000000,3000000" />
      </div>
      <div class="form-group">
        <label for="biayaVariabel">Biaya Variabel (%):</label>
        <input type="number" v-model="biayaVariabel" id="biayaVariabel" required />
      </div>
      <div class="form-group">
        <label for="biayaTetap">Biaya Tetap (Rp):</label>
        <input type="number" v-model="biayaTetap" id="biayaTetap" required />
      </div>
      <div class="form-group">
        <label for="pajak">Pajak (%):</label>
        <input type="number" v-model="pajak" id="pajak" required />
      </div>
      <div class="form-group">
        <label for="modalKerja">Modal Kerja (Rp):</label>
        <input type="number" v-model="modalKerja" id="modalKerja" required />
      </div>
      <div class="form-group">
        <label for="umurEkonomis">Umur Ekonomis (Tahun):</label>
        <input type="number" v-model="umurEkonomis" id="umurEkonomis" required />
      </div>
      <div class="form-group">
        <label for="danaInvestasi">Dana Investasi (Rp):</label>
        <input type="number" v-model="danaInvestasi" id="danaInvestasi" required />
      </div>
      <!-- Tombol Hitung -->
      <button type="button" @click="calculate" class="button">Hitung</button>
    </form>

    <!-- Menampilkan Hasil -->
    <div v-if="result" class="results">
      <h2>Hasil Perhitungan</h2>
      <div class="result-item">
        <p><strong>Total Penjualan (Rp):</strong> {{ totalPenjualan }}</p>
        <p><strong>IRR:</strong> {{ result.irr }}%</p>
        <p><strong>NPV:</strong> {{ result.npv }} </p>
        <p v-if="result.pp < umurEkonomis"><strong>PP:</strong> {{ result.pp }} Tahun (Mencapai Balik Modal)</p>
        <p v-else-if="result.pp > umurEkonomis"><strong>PP:</strong> {{ result.pp }} Tahun (Belum Mencapai Balik Modal)</p>
        <p v-if="result.arr < biayaVariabel"><strong>ARR:</strong> {{ result.arr }}% (Tidak Layak)</p>
        <p v-else-if="result.arr > biayaVariabel"><strong>ARR:</strong> {{ result.arr }}% (Layak)</p>
        <p v-if="result.pi < 0"><strong>PI:</strong> {{ result.pi }} Tidak Layak</p>
        <p v-else-if="result.pi > 0"><strong>PI:</strong> {{ result.pi }} Layak</p>
        
        
        
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      penjualanInput: '',
      biayaVariabel: 0,
      biayaTetap: 0,
      pajak: 0,
      modalKerja: 0,
      umurEkonomis: 0,
      danaInvestasi: 0,
      result: null,
      totalPenjualan: 0
    };
  },
  methods: {
    calculate() {
      const penjualanValues = this.penjualanInput.split(',').map(item => parseFloat(item.trim()));
      
      // Menghitung total penjualan
      this.totalPenjualan = penjualanValues.reduce((acc, val) => acc + val, 0);

      const { biayaVariabel, biayaTetap, pajak, modalKerja, umurEkonomis, danaInvestasi } = this;

      // Menghitung Laba dan Cash Flow berdasarkan total penjualan
      const labaTahun = this.totalPenjualan * (1 - biayaVariabel / 100) - biayaTetap;
      const labaSetelahPajak = labaTahun * (1 - pajak / 100);
      const cashFlowTahun = labaSetelahPajak + modalKerja;

      // Menghitung NPV, IRR, PP, ARR, PI berdasarkan total penjualan
      const npv = this.calculateNPV(danaInvestasi, cashFlowTahun, umurEkonomis);
      const irr = this.calculateIRR(danaInvestasi, cashFlowTahun, umurEkonomis);
      const pp = this.calculatePP(danaInvestasi, cashFlowTahun);
      const arr = (labaSetelahPajak / danaInvestasi) * 100;
      const pi = npv / danaInvestasi;

      // Menyimpan hasil dalam satu objek
      this.result = {
        irr: irr.toFixed(2),
        npv: npv.toFixed(2),
        pp: pp.toFixed(2),
        arr: arr.toFixed(2),
        pi: pi.toFixed(2)
      };
    },

    calculateNPV(danaInvestasi, cashFlowTahun, umur) {
      let npv = -danaInvestasi;
      const discountRate = 0.1;
      for (let t = 1; t <= umur; t++) {
        npv += cashFlowTahun / Math.pow(1 + discountRate, t);
      }
      return npv;
    },

    calculateIRR(danaInvestasi, cashFlowTahun, umur) {
      let irr = 0.1;
      let npv = this.calculateNPV(danaInvestasi, cashFlowTahun, umur);
      while (npv > 0) {
        irr += 0.01;
        npv = this.calculateNPV(danaInvestasi, cashFlowTahun, umur);
      }
      return irr * 100;
    },

    calculatePP(danaInvestasi, cashFlowTahun) {
      return danaInvestasi / cashFlowTahun;
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.title {
  text-align: center;
  margin-bottom: 30px;
}

.title h1 {
  font-size: 24px;
  font-weight: bold;
  color: #333;
}

.title p {
  font-size: 16px;
  color: #555;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 15px;
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 5px;
  font-weight: bold;
}

input {
  padding: 8px;
  font-size: 14px;
  border-radius: 4px;
  border: 1px solid #ccc;
}

button {
  padding: 10px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  margin-top: 15px;
}

button:hover {
  background-color: #369e6b;
}

.results {
  margin-top: 30px;
}

.result-item {
  background-color: #f1f1f1;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 15px;
}

.result-item p {
  margin: 5px 0;
}

h2 {
  text-align: center;
  color: #333;
}
</style>
