# nodejs

const { spawn } = require('child_process');
const fs = require('fs');

const namaFileLog = 'log.txt';
const namaFileBash = 'tes.sh';

const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));

const run1 = async () => {
  // Menulis skrip Bash ke file
  fs.writeFileSync(namaFileBash, `ssh -o StrictHostKeyChecking=no -o ServerAliveInterval=60 -R 80:localhost:8080 serveo.net > ${namaFileLog}`);

  // Menjalankan skrip Bash di latar belakang
  const childProcess = spawn('bash', [namaFileBash], { detached: true, stdio: 'ignore' });
  childProcess.unref();

  // Menunggu 5 detik untuk memastikan skrip Bash berjalan
  await sleep(5000);

  // Membaca isi file log.txt setelah skrip selesai
  fs.readFile(namaFileLog, 'utf8', (readError, data) => {
    if (readError) {
      console.error(`Error reading file: ${readError.message}`);
      return;
    }

    console.log(`Isi file ${namaFileLog}:`);
    console.log(data);
  });
};

const run2 = async () => {
  while (true) {
    // Menghapus file tes.sh dan log.txt jika salah satu atau keduanya ada
    if (fs.existsSync(namaFileBash) || fs.existsSync(namaFileLog)) {
      try {
        if (fs.existsSync(namaFileBash)) {
          fs.unlinkSync(namaFileBash);
        }

        if (fs.existsSync(namaFileLog)) {
          fs.unlinkSync(namaFileLog);
        }
      } finally {
        // Tidak ada tindakan khusus saat terjadi kesalahan
      }
    }

    // Menunggu 5 detik sebelum iterasi selanjutnya
    await sleep(5000);
  }
};

run1();
run2();
