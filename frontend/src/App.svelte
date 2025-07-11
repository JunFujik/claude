<script>
  import UserManagement from './components/UserManagement.svelte';
  import QRGenerator from './components/QRGenerator.svelte';
  import QRScanner from './components/QRScanner.svelte';
  import AttendanceList from './components/AttendanceList.svelte';

  // --- 管理者機能用の変数 ---
  let isAdminView = false;
  // 仮の管理者パスワード (本来は環境変数などで管理するのが望ましい)
  const ADMIN_PASSWORD = "password"; 
  onMount(fetchUsers);

  async function fetchUsers() {
    try {
      const response = await userAPI.getAll();
      users = response.data;
    } catch (error) {
      handleError(error, 'ユーザーの取得に失敗しました');
    }
  }

  function handleError(error, defaultMessage) {
    errorMessage = error.response?.data?.error || defaultMessage;
    setTimeout(() => errorMessage = '', 3000);
  }

  function handleSuccess(message) {
    successMessage = message;
    setTimeout(() => successMessage = '', 3000);
  }
  
  /**
   * 管理者ログインを処理する関数
   */
  function handleAdminLogin() {
    if (isAdminView) {
      isAdminView = false;
      return;
    }

    const input = prompt('管理者パスワードを入力してください:');
    if (input === ADMIN_PASSWORD) {
      isAdminView = true;
      handleSuccess('管理者としてログインしました。');
    } else if (input !== null) { // キャンセルされなかった場合のみエラー表示
      alert('パスワードが違います。');
    }
  }

  let activeTab = 'qr-scan';

  const tabs = [
    { id: 'qr-scan', label: 'QRコード読取' },
    // いずれ下の機能はこの画面から消すこと
    { id: 'users', label: 'ユーザー管理' },
    { id: 'qr-generate', label: 'QRコード生成' },
    { id: 'attendance', label: '勤怠記録' }
  ];
</script>

<main>
  
  <h1>勤怠管理システム</h1>

  <div class="header">
  <h1>勤怠管理システム</h1>
    <button on:click={handleAdminLogin}>
      {isAdminView ? 'ログアウト' : '管理者としてログイン'}
    </button>
  </div>
  
  <div class="tab-buttons">
    {#each tabs as tab}
      <button 
        class="tab-button" 
        class:active={activeTab === tab.id}
        on:click={() => activeTab = tab.id}
      >
        {tab.label}
      </button>
    {/each}
  </div>

  <div class="tab-content">
    {#if isAdminView}
      <div class="card">
        <h2>管理者ダッシュボード</h2>
        <p>ここでは管理者向けの情報を表示します。</p>
      </div>
    {:else if activeTab === 'qr-scan'}
      <QRScanner />
    {:else if activeTab === 'users'}
      <UserManagement />
    {:else if activeTab === 'qr-generate'}
      <QRGenerator />
    {:else if activeTab === 'attendance'}
      <AttendanceList />
    {/if}
  </div>
</main>

<style>
  main {
    text-align: center;
  }

  h1 {
    color: #333;
    margin-bottom: 2rem;
  }

  .tab-content {
    text-align: left;
  }
</style>