<script>
  import { qrAPI, attendanceAPI } from '../lib/api';

  export let users = [];
  export let selectedUserIdForQR = '';

  let qrCodeDataUrl = '';
  let qrData = '';
  let qrInput = '';
  let checkStatus = '';
  let checkError = '';

  async function handleScanSubmit() {
    if (!qrInput) return;
    checkStatus = '';
    checkError = '';
    try {
      const response = await attendanceAPI.check({ qr_data: qrInput });
      checkStatus = response.data.message;
      qrInput = ''; // Reset input after successful scan
    } catch (error) {
      checkError = error.response?.data?.error || '予期せぬエラーが発生しました。';
    }
  }
</script>

<div class="card">
  <h2>QRコード読み取り (USBスキャナ)</h2>
  <form on:submit|preventDefault={handleScanSubmit}>
    <div class="form-group">
      <label for="qr-input">ここにスキャンしたQRコードの内容が表示されます</label>
      <input type="text" id="qr-input" bind:value={qrInput} placeholder="USBスキャナでQRコードをスキャン..." autofocus />
    </div>
    <button type="submit">打刻する</button>
  </form>
  {#if checkStatus}
    <p class="success">{checkStatus}</p>
  {/if}
  {#if checkError}
    <p class="error">{checkError}</p>
  {/if}
</div>
