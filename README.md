# Eth-wallet-tracker
Built a simple dApp that connects to MetaMask and tracks balances of Ethereum wallets.


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ethereum Wallet Tracker</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/web3/1.10.0/web3.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            color: #333;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            text-align: center;
            margin-bottom: 40px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .header h1 {
            color: white;
            font-size: 2.5rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .header p {
            color: rgba(255, 255, 255, 0.8);
            font-size: 1.1rem;
        }

        .connection-section {
            background: white;
            border-radius: 15px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .btn {
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 600;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }

        .btn:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
        }

        .wallet-info {
            background: rgba(102, 126, 234, 0.1);
            border-radius: 10px;
            padding: 20px;
            margin: 20px 0;
            border-left: 4px solid #667eea;
        }

        .wallet-info h3 {
            color: #667eea;
            margin-bottom: 10px;
        }

        .wallet-info p {
            margin: 5px 0;
            font-family: 'Courier New', monospace;
            word-break: break-all;
        }

        .tracker-section {
            background: white;
            border-radius: 15px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
        }

        .input-group {
            margin-bottom: 20px;
        }

        .input-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #333;
        }

        .input-group input {
            width: 100%;
            padding: 12px;
            border: 2px solid #e1e1e1;
            border-radius: 10px;
            font-size: 1rem;
            font-family: 'Courier New', monospace;
            transition: border-color 0.3s ease;
        }

        .input-group input:focus {
            outline: none;
            border-color: #667eea;
        }

        .balance-card {
            background: linear-gradient(45deg, #f093fb 0%, #f5576c 100%);
            color: white;
            border-radius: 15px;
            padding: 25px;
            margin: 15px 0;
            text-align: center;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
            transform: scale(1);
            transition: transform 0.3s ease;
        }

        .balance-card:hover {
            transform: scale(1.02);
        }

        .balance-card h4 {
            margin-bottom: 10px;
            font-size: 1.2rem;
        }

        .balance-card .amount {
            font-size: 2rem;
            font-weight: bold;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .balance-card .address {
            font-size: 0.9rem;
            opacity: 0.8;
            font-family: 'Courier New', monospace;
            word-break: break-all;
            margin-top: 10px;
        }

        .tracked-wallets {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .error {
            background: linear-gradient(45deg, #ff6b6b, #ee5a24);
            color: white;
            padding: 15px;
            border-radius: 10px;
            margin: 10px 0;
            text-align: center;
        }

        .loading {
            text-align: center;
            padding: 20px;
            color: #667eea;
            font-weight: 600;
        }

        .remove-btn {
            background: #e74c3c;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 0.8rem;
            margin-top: 10px;
            transition: background 0.3s ease;
        }

        .remove-btn:hover {
            background: #c0392b;
        }

        .network-info {
            background: rgba(118, 75, 162, 0.1);
            border-radius: 10px;
            padding: 15px;
            margin: 15px 0;
            border-left: 4px solid #764ba2;
        }

        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin: 20px 0;
        }

        .stat-card {
            background: white;
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        }

        .stat-card h4 {
            color: #667eea;
            margin-bottom: 10px;
        }

        .stat-card .value {
            font-size: 1.5rem;
            font-weight: bold;
            color: #333;
        }

        @media (max-width: 768px) {
            .header h1 {
                font-size: 2rem;
            }
            
            .tracked-wallets {
                grid-template-columns: 1fr;
            }
            
            .stats {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🦊 Ethereum Wallet Tracker</h1>
            <p>Connect your MetaMask wallet and track Ethereum balances across multiple addresses</p>
        </div>
<div class="connection-section">
            <h2>MetaMask Connection</h2>
            <button id="connectBtn" class="btn">Connect MetaMask</button>
            <div id="connectionStatus"></div>
            <div id="networkInfo"></div>
        </div>

        <div class="tracker-section">
            <h2>Track Wallet Balances</h2>
            <div class="input-group">
                <label for="addressInput">Enter Ethereum Address to Track:</label>
                <input type="text" id="addressInput" placeholder="0x..." maxlength="42">
            </div>
            <button id="addWalletBtn" class="btn">Add Wallet</button>
            <button id="refreshAllBtn" class="btn">Refresh All Balances</button>
            
            <div id="error"></div>
            <div id="loading"></div>
            
            <div class="stats">
                <div class="stat-card">
                    <h4>Total Wallets</h4>
                    <div class="value" id="totalWallets">0</div>
                </div>
                <div class="stat-card">
                    <h4>Total Balance</h4>
                    <div class="value" id="totalBalance">0.00 ETH</div>
                </div>
                <div class="stat-card">
                    <h4>Network</h4>
                    <div class="value" id="currentNetwork">Not Connected</div>
                </div>
            </div>
            
            <div id="trackedWallets" class="tracked-wallets"></div>
        </div>
    </div>

    <script>
        class EthereumWalletTracker {
            constructor() {
                this.web3 = null;
                this.userAccount = null;
                this.trackedWallets = [];
                this.networkId = null;
                this.networkName = '';
                
                this.init();
            }

            init() {
                this.bindEvents();
                this.checkMetaMaskConnection();
                this.loadTrackedWallets();
            }

            bindEvents() {
                document.getElementById('connectBtn').addEventListener('click', () => this.connectMetaMask());
                document.getElementById('addWalletBtn').addEventListener('click', () => this.addWallet());
                document.getElementById('refreshAllBtn').addEventListener('click', () => this.refreshAllBalances());
                
                // Listen for account changes
                if (window.ethereum) {
                    window.ethereum.on('accountsChanged', (accounts) => {
                        if (accounts.length === 0) {
                            this.handleDisconnection();
                        } else {
                            this.userAccount = accounts[0];
                            this.updateConnectionStatus();
                        }
                    });
                    
                    window.ethereum.on('chainChanged', (chainId) => {
                        this.networkId = parseInt(chainId, 16);
                        this.updateNetworkInfo();
                    });
                }
            }

            async checkMetaMaskConnection() {
                if (typeof window.ethereum !== 'undefined') {
                    try {
                        const accounts = await window.ethereum.request({ method: 'eth_accounts' });
                        if (accounts.length > 0) {
                            this.web3 = new Web3(window.ethereum);
                            this.userAccount = accounts[0];
                            this.networkId = await this.web3.eth.net.getId();
                            this.updateConnectionStatus();
                            this.updateNetworkInfo();
                        }
                    } catch (error) {
                        console.error('Error checking MetaMask connection:', error);
                    }
                } else {
                    this.showError('MetaMask is not installed. Please install MetaMask to use this dApp.');
                }
            }

            async connectMetaMask() {
                if (typeof window.ethereum !== 'undefined') {
                    try {
                        const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
                        this.web3 = new Web3(window.ethereum);
                        this.userAccount = accounts[0];
                        this.networkId = await this.web3.eth.net.getId();
                        
                        this.updateConnectionStatus();
                        this.updateNetworkInfo();
                        this.clearError();
                    } catch (error) {
                        this.showError('Failed to connect to MetaMask: ' + error.message);
                    }
                } else {
                    this.showError('MetaMask is not installed. Please install MetaMask to use this dApp.');
                }
            }

            updateConnectionStatus() {
                const statusDiv = document.getElementById('connectionStatus');
                if (this.userAccount) {
                    statusDiv.innerHTML = `
                        <div class="wallet-info">
                            <h3>Connected Wallet</h3>
                            <p><strong>Address:</strong> ${this.userAccount}</p>
                            <p><strong>Network ID:</strong> ${this.networkId}</p>
                        </div>
                    `;
                    document.getElementById('connectBtn').textContent = 'Connected';
                    document.getElementById('connectBtn').disabled = true;
                } else {
                    statusDiv.innerHTML = '';
                    document.getElementById('connectBtn').textContent = 'Connect MetaMask';
                    document.getElementById('connectBtn').disabled = false;
                }
            }

            updateNetworkInfo() {
                const networkNames = {
                    1: 'Ethereum Mainnet',
                    5: 'Goerli Testnet',
                    11155111: 'Sepolia Testnet',
                    137: 'Polygon Mainnet',
                    80001: 'Polygon Mumbai',
                    56: 'BSC Mainnet',
                    97: 'BSC Testnet'
                };
                
                this.networkName = networkNames[this.networkId] || `Unknown Network (${this.networkId})`;
                document.getElementById('currentNetwork').textContent = this.networkName;
                
                const networkDiv = document.getElementById('networkInfo');
                networkDiv.innerHTML = `
                    <div class="network-info">
                        <strong>Current Network:</strong> ${this.networkName}
                    </div>
                `;
            }

            handleDisconnection() {
                this.userAccount = null;
                this.web3 = null;
                this.updateConnectionStatus();
                this.showError('MetaMask disconnected. Please reconnect to continue.');
            }

            async addWallet() {
                const addressInput = document.getElementById('addressInput');
                const address = addressInput.value.trim();
                
                if (!address) {
                    this.showError('Please enter a valid Ethereum address');
                    return;
                }
                
                if (!this.web3) {
                    this.showError('Please connect MetaMask first');
                    return;
                }
                
                if (!this.web3.utils.isAddress(address)) {
                    this.showError('Invalid Ethereum address format');
                    return;
                }
                
                if (this.trackedWallets.some(wallet => wallet.address.toLowerCase() === address.toLowerCase())) {
                    this.showError('This address is already being tracked');
                    return;
                }
                
                try {
                    this.showLoading('Adding wallet...');
                    const balance = await this.getBalance(address);
                    
                    const wallet = {
                        address: address,
                        balance: balance,
                        balanceEth: this.web3.utils.fromWei(balance, 'ether'),
                        lastUpdated: new Date().toLocaleString()
                    };
                    
                    this.trackedWallets.push(wallet);
                    this.saveTrackedWallets();
                    this.renderTrackedWallets();
                    this.updateStats();
                    
                    addressInput.value = '';
                    this.clearError();
                    this.clearLoading();
                } catch (error) {
                    this.showError('Failed to add wallet: ' + error.message);
                    this.clearLoading();
                }
            }

            async getBalance(address) {
                try {
                    const balance = await this.web3.eth.getBalance(address);
                    return balance;
                } catch (error) {
                    throw new Error('Failed to fetch balance for ' + address);
                }
            }

            async refreshAllBalances() {
                if (!this.web3) {
                    this.showError('Please connect MetaMask first');
                    return;
                }
                
                if (this.trackedWallets.length === 0) {
                    this.showError('No wallets to refresh');
                    return;
                }
                
                try {
                    this.showLoading('Refreshing all balances...');
                    
                    for (let i = 0; i < this.trackedWallets.length; i++) {
                        const wallet = this.trackedWallets[i];
                        const balance = await this.getBalance(wallet.address);
                        
                        wallet.balance = balance;
                        wallet.balanceEth = this.web3.utils.fromWei(balance, 'ether');
                        wallet.lastUpdated = new Date().toLocaleString();
                    }
                    
                    this.saveTrackedWallets();
                    this.renderTrackedWallets();
                    this.updateStats();
                    this.clearLoading();
                    this.clearError();
                } catch (error) {
                    this.showError('Failed to refresh balances: ' + error.message);
                    this.clearLoading();
                }
            }

            removeWallet(address) {
                this.trackedWallets = this.trackedWallets.filter(wallet => wallet.address !== address);
                this.saveTrackedWallets();
                this.renderTrackedWallets();
                this.updateStats();
            }

            renderTrackedWallets() {
                const container = document.getElementById('trackedWallets');
                
                if (this.trackedWallets.length === 0) {
                    container.innerHTML = '<p style="text-align: center; color: #666; margin-top: 40px;">No wallets tracked yet. Add some addresses to get started!</p>';
                    return;
                }
                
                container.innerHTML = this.trackedWallets.map(wallet => `
                    <div class="balance-card">
                        <h4>Wallet Balance</h4>
                        <div class="amount">${parseFloat(wallet.balanceEth).toFixed(4)} ETH</div>
                        <div class="address">${wallet.address}</div>
                        <p style="margin-top: 10px; font-size: 0.8rem; opacity: 0.7;">
                            Last updated: ${wallet.lastUpdated}
                        </p>
                        <button class="remove-btn" onclick="walletTracker.removeWallet('${wallet.address}')">
                            Remove
                        </button>
                    </div>
                `).join('');
            }

            updateStats() {
                document.getElementById('totalWallets').textContent = this.trackedWallets.length;
                
                const totalBalance = this.trackedWallets.reduce((sum, wallet) => {
                    return sum + parseFloat(wallet.balanceEth);
                }, 0);
                
                document.getElementById('totalBalance').textContent = `${totalBalance.toFixed(4)} ETH`;
            }

            saveTrackedWallets() {
                // In a real application, you'd save to localStorage or a database
                // For this demo, we'll just keep them in memory
                console.log('Tracked wallets saved:', this.trackedWallets);
            }

            loadTrackedWallets() {
                // In a real application, you'd load from localStorage or a database
                // For this demo, we'll start with an empty array
                this.trackedWallets = [];
                this.renderTrackedWallets();
                this.updateStats();
            }

            showError(message) {
                const errorDiv = document.getElementById('error');
                errorDiv.innerHTML = `<div class="error">${message}</div>`;
                setTimeout(() => this.clearError(), 5000);
            }

            clearError() {
                document.getElementById('error').innerHTML = '';
            }

            showLoading(message) {
                const loadingDiv = document.getElementById('loading');
                loadingDiv.innerHTML = `<div class="loading">${message}</div>`;
            }

            clearLoading() {
                document.getElementById('loading').innerHTML = '';
            }
        }

        // Initialize the wallet tracker
        const walletTracker = new EthereumWalletTracker();
    </script>
</body>
</html>
