# APM

## Client Available
- Java
- RUM (JS)
- NodeJS
- Django
- Flask
- Ruby on Rails
- Rack
- Go
- .NET
- PHP
- OpenTelemetry

## Prerequisites
- Fleet Server
- Elastic Agent (Can be the same with Fleet Server or Standalone)
- Elastic APM Library (For Client App)

## Step 1
#### Add APM integration to Agent Policy

#### Select IP, URL and Port (Client will connect to APM URL)

#### [Optinal] TLS and Certificate Secret Token (Disable by now)

## Step 2
#### In Client Application, Setup the URL, Token

---
<details>
  <summary>Example Client App</summary>
  
  ##### This app will send transaction every 10 seconds (Adjustable) in fixed mode, and send transaction between 3 to 30 seconds (Adjustable) in random mode. Each times has 10% chance to send error transaction.
  ```js
  // server.js - Minimal APM Transaction Generator
  
  // IMPORTANT: APM agent must be imported FIRST
  const apm = require('elastic-apm-node').start({
    serviceName: 'transaction-generator',
    serverUrl: process.env.ELASTIC_APM_SERVER_URL || 'http://localhost:8200',
    environment: process.env.NODE_ENV || 'development',
    secretToken: process.env.ELASTIC_APM_SECRET_TOKEN,
    transactionSampleRate: 1.0,
    logLevel: 'info'
  });
  
  const express = require('express');
  const app = express();
  const port = process.env.PORT || 3000;
  const host = process.env.HOST || 'localhost'
  
  // State management
  let regularIntervalId = null;
  let randomIntervalId = null;
  let randomTransactionEnabled = false;
  let transactionCounter = 0;
  
  // Duration configuration
  let durationMode = 'fixed'; // 'fixed' or 'random'
  let fixedDuration = 10000;   // 10 seconds in ms
  let randomMinDuration = 3000;  // 3 seconds in ms
  let randomMaxDuration = 30000; // 30 seconds in ms
  
  // Transaction types for variety
  const transactionTypes = [
    'payment_processing',
    'user_registration',
    'data_sync',
    'file_upload',
    'email_send',
    'report_generation',
    'cache_update',
    'api_call'
  ];
  
  // Get duration based on current mode
  function getDuration() {
    if (durationMode === 'fixed') {
      return fixedDuration;
    } else {
      return Math.random() * (randomMaxDuration - randomMinDuration) + randomMinDuration;
    }
  }
  
  // Generate a random transaction
  function generateTransaction(type = 'scheduled') {
    const transactionType = transactionTypes[Math.floor(Math.random() * transactionTypes.length)];
    const transaction = apm.startTransaction(`${transactionType}_${type}`, 'background');
    
    if (transaction) {
      const startTime = Date.now();
      const duration = getDuration();
      
      // Add some labels for better tracking
      transaction.setLabel('transaction_type', transactionType);
      transaction.setLabel('generation_type', type);
      transaction.setLabel('counter', ++transactionCounter);
      transaction.setLabel('duration_mode', durationMode);
      transaction.setLabel('duration_ms', Math.round(duration));
      
      // Create some spans within the transaction
      const span1 = apm.startSpan('initialization');
      if (span1) {
        setTimeout(() => {
          span1.setLabel('init_time', Date.now() - startTime);
          span1.end();
          
          const span2 = apm.startSpan('main_processing');
          if (span2) {
            setTimeout(() => {
              span2.setLabel('processing_time', Date.now() - startTime);
              span2.end();
              
              // Randomly add errors (10% chance)
              if (Math.random() < 0.1) {
                const error = new Error(`Random error in ${transactionType}`);
                apm.captureError(error, {
                  custom: {
                    transaction_type: transactionType,
                    generation_type: type,
                    counter: transactionCounter,
                    duration_mode: durationMode
                  }
                });
                transaction.result = 'error';
              } else {
                transaction.result = 'success';
              }
              
              transaction.end();
              console.log(`✓ Transaction #${transactionCounter}: ${transactionType} (${type}) - ${durationMode} mode - ${Math.round(duration)}ms - ${Date.now() - startTime}ms actual`);
            }, duration * 0.7);
          }
        }, duration * 0.3);
      }
    }
  }
  
  // Start regular 10-second interval transactions
  function startRegularTransactions() {
    if (regularIntervalId) return;
    
    console.log('🚀 Starting regular 10-second interval transactions...');
    regularIntervalId = setInterval(() => {
      generateTransaction('scheduled');
    }, 10000);
    
    // Generate first transaction immediately
    generateTransaction('scheduled');
  }
  
  // Stop regular transactions
  function stopRegularTransactions() {
    if (regularIntervalId) {
      clearInterval(regularIntervalId);
      regularIntervalId = null;
      console.log('⏹️  Stopped regular transactions');
    }
  }
  
  // Start random transactions (1-30 second intervals)
  function startRandomTransactions() {
    if (randomIntervalId) return;
    
    randomTransactionEnabled = true;
    console.log('🎲 Starting random interval transactions (1-30 seconds)...');
    
    function scheduleNextRandomTransaction() {
      if (!randomTransactionEnabled) return;
      
      const randomDelay = (Math.random() * 29 + 1) * 1000; // 1-30 seconds in ms
      randomIntervalId = setTimeout(() => {
        if (randomTransactionEnabled) {
          generateTransaction('random');
          scheduleNextRandomTransaction();
        }
      }, randomDelay);
      
      console.log(`⏰ Next random transaction in ${Math.round(randomDelay/1000)} seconds`);
    }
    
    scheduleNextRandomTransaction();
  }
  
  // Stop random transactions
  function stopRandomTransactions() {
    randomTransactionEnabled = false;
    if (randomIntervalId) {
      clearTimeout(randomIntervalId);
      randomIntervalId = null;
      console.log('⏹️  Stopped random transactions');
    }
  }
  
  // Middleware
  app.use(express.json());
  app.use(express.static('public')); // For serving HTML interface
  
  // API Routes
  app.get('/', (req, res) => {
    res.send(`
      <!DOCTYPE html>
      <html>
      <head>
          <title>APM Transaction Generator</title>
          <style>
              body { font-family: Arial, sans-serif; margin: 40px; }
              .container { max-width: 800px; margin: 0 auto; }
              .status { padding: 20px; background: #f5f5f5; border-radius: 5px; margin: 20px 0; }
              .controls { margin: 20px 0; }
              button { padding: 10px 20px; margin: 5px; border: none; border-radius: 3px; cursor: pointer; }
              .start { background: #4CAF50; color: white; }
              .stop { background: #f44336; color: white; }
              .info { background: #2196F3; color: white; }
              .logs { background: #000; color: #0f0; padding: 20px; border-radius: 5px; height: 300px; overflow-y: auto; }
          </style>
      </head>
      <body>
          <div class="container">
              <h1>🔥 APM Transaction Generator</h1>
              
              <div class="status">
                  <h3>Status</h3>
                  <p><strong>Regular Transactions (10s):</strong> <span id="regularStatus">${regularIntervalId ? 'Running' : 'Stopped'}</span></p>
                  <p><strong>Random Transactions (1-30s):</strong> <span id="randomStatus">${randomTransactionEnabled ? 'Running' : 'Stopped'}</span></p>
                  <p><strong>Duration Mode:</strong> <span id="durationMode">${durationMode}</span></p>
                  <p><strong>Fixed Duration:</strong> <span id="fixedDuration">${fixedDuration/1000}s</span></p>
                  <p><strong>Random Duration Range:</strong> <span id="randomRange">${randomMinDuration/1000}s - ${randomMaxDuration/1000}s</span></p>
                  <p><strong>Total Transactions Generated:</strong> <span id="counter">${transactionCounter}</span></p>
              </div>
              
              <div class="controls">
                  <h3>Duration Settings</h3>
                  <button class="info" onclick="setDurationMode('fixed')">Set Fixed Duration Mode</button>
                  <button class="info" onclick="setDurationMode('random')">Set Random Duration Mode</button>
                  <br><br>
                  <label>Fixed Duration (seconds): </label>
                  <input type="number" id="fixedDurationInput" value="${fixedDuration/1000}" min="1" max="300">
                  <button class="info" onclick="updateFixedDuration()">Update</button>
                  <br><br>
                  <label>Random Min (seconds): </label>
                  <input type="number" id="randomMinInput" value="${randomMinDuration/1000}" min="1" max="300">
                  <label> Max (seconds): </label>
                  <input type="number" id="randomMaxInput" value="${randomMaxDuration/1000}" min="1" max="300">
                  <button class="info" onclick="updateRandomRange()">Update</button>
              </div>
              
              <div class="controls">
                  <h3>Transaction Controls</h3>
                  <button class="start" onclick="toggleRegular()">Toggle Regular Transactions</button>
                  <button class="start" onclick="toggleRandom()">Toggle Random Transactions</button>
                  <button class="info" onclick="generateManual()">Generate Manual Transaction</button>
                  <button class="info" onclick="updateStatus()">Refresh Status</button>
              </div>
              
              <div class="controls">
                  <h3>Quick Actions</h3>
                  <button class="start" onclick="startAll()">Start All</button>
                  <button class="stop" onclick="stopAll()">Stop All</button>
              </div>
          </div>
          
          <script>
              function setDurationMode(mode) {
                  fetch('/api/duration-mode/' + mode, { method: 'POST' })
                      .then(() => updateStatus());
              }
              
              function updateFixedDuration() {
                  const duration = document.getElementById('fixedDurationInput').value;
                  fetch('/api/fixed-duration', {
                      method: 'POST',
                      headers: { 'Content-Type': 'application/json' },
                      body: JSON.stringify({ duration: parseFloat(duration) })
                  }).then(() => updateStatus());
              }
              
              function updateRandomRange() {
                  const min = document.getElementById('randomMinInput').value;
                  const max = document.getElementById('randomMaxInput').value;
                  fetch('/api/random-range', {
                      method: 'POST',
                      headers: { 'Content-Type': 'application/json' },
                      body: JSON.stringify({ 
                          min: parseFloat(min), 
                          max: parseFloat(max) 
                      })
                  }).then(() => updateStatus());
              }
              
              function toggleRegular() {
                  fetch('/api/toggle-regular', { method: 'POST' })
                      .then(() => updateStatus());
              }
              
              function toggleRandom() {
                  fetch('/api/toggle-random', { method: 'POST' })
                      .then(() => updateStatus());
              }
              
              function generateManual() {
                  fetch('/api/generate-manual', { method: 'POST' })
                      .then(() => updateStatus());
              }
              
              function startAll() {
                  fetch('/api/start-all', { method: 'POST' })
                      .then(() => updateStatus());
              }
              
              function stopAll() {
                  fetch('/api/stop-all', { method: 'POST' })
                      .then(() => updateStatus());
              }
              
              function updateStatus() {
                  fetch('/api/status')
                      .then(res => res.json())
                      .then(data => {
                          document.getElementById('regularStatus').textContent = data.regularRunning ? 'Running' : 'Stopped';
                          document.getElementById('randomStatus').textContent = data.randomRunning ? 'Running' : 'Stopped';
                          document.getElementById('durationMode').textContent = data.durationMode;
                          document.getElementById('fixedDuration').textContent = (data.fixedDuration/1000) + 's';
                          document.getElementById('randomRange').textContent = (data.randomMinDuration/1000) + 's - ' + (data.randomMaxDuration/1000) + 's';
                          document.getElementById('counter').textContent = data.transactionCounter;
                          
                          // Update input fields
                          document.getElementById('fixedDurationInput').value = data.fixedDuration/1000;
                          document.getElementById('randomMinInput').value = data.randomMinDuration/1000;
                          document.getElementById('randomMaxInput').value = data.randomMaxDuration/1000;
                      });
              }
              
              // Auto refresh status every 5 seconds
              setInterval(updateStatus, 5000);
          </script>
      </body>
      </html>
    `);
  });
  
  // API endpoints
  app.get('/api/status', (req, res) => {
    res.json({
      regularRunning: !!regularIntervalId,
      randomRunning: randomTransactionEnabled,
      transactionCounter: transactionCounter,
      apmEnabled: apm.isStarted(),
      durationMode: durationMode,
      fixedDuration: fixedDuration,
      randomMinDuration: randomMinDuration,
      randomMaxDuration: randomMaxDuration
    });
  });
  
  app.post('/api/duration-mode/:mode', (req, res) => {
    const { mode } = req.params;
    if (mode === 'fixed' || mode === 'random') {
      durationMode = mode;
      console.log(`🔧 Duration mode changed to: ${mode}`);
      res.json({ success: true, mode: durationMode });
    } else {
      res.status(400).json({ error: 'Invalid mode. Use "fixed" or "random"' });
    }
  });
  
  app.post('/api/fixed-duration', (req, res) => {
    const { duration } = req.body;
    if (duration && duration > 0) {
      fixedDuration = duration * 1000; // Convert to milliseconds
      console.log(`🔧 Fixed duration changed to: ${duration}s`);
      res.json({ success: true, fixedDuration: fixedDuration });
    } else {
      res.status(400).json({ error: 'Invalid duration' });
    }
  });
  
  app.post('/api/random-range', (req, res) => {
    const { min, max } = req.body;
    if (min && max && min > 0 && max > min) {
      randomMinDuration = min * 1000; // Convert to milliseconds
      randomMaxDuration = max * 1000; // Convert to milliseconds
      console.log(`🔧 Random duration range changed to: ${min}s - ${max}s`);
      res.json({ 
        success: true, 
        randomMinDuration: randomMinDuration,
        randomMaxDuration: randomMaxDuration 
      });
    } else {
      res.status(400).json({ error: 'Invalid range. Min must be positive and less than max' });
    }
  });
  
  app.post('/api/toggle-regular', (req, res) => {
    if (regularIntervalId) {
      stopRegularTransactions();
    } else {
      startRegularTransactions();
    }
    res.json({ success: true, running: !!regularIntervalId });
  });
  
  app.post('/api/toggle-random', (req, res) => {
    if (randomTransactionEnabled) {
      stopRandomTransactions();
    } else {
      startRandomTransactions();
    }
    res.json({ success: true, running: randomTransactionEnabled });
  });
  
  app.post('/api/generate-manual', (req, res) => {
    generateTransaction('manual');
    res.json({ success: true, counter: transactionCounter });
  });
  
  app.post('/api/start-all', (req, res) => {
    startRegularTransactions();
    startRandomTransactions();
    res.json({ success: true });
  });
  
  app.post('/api/stop-all', (req, res) => {
    stopRegularTransactions();
    stopRandomTransactions();
    res.json({ success: true });
  });
  
  // Health check
  app.get('/health', (req, res) => {
    res.json({
      status: 'healthy',
      apm_enabled: apm.isStarted(),
      regular_transactions: !!regularIntervalId,
      random_transactions: randomTransactionEnabled,
      total_transactions: transactionCounter
    });
  });
  
  // Start server
  app.listen(port, host, () => {
    console.log(`🌟 APM Transaction Generator running at ${host}:${port}`);
    console.log(`🌐 Access URL: ${host}:${port}`);
    console.log(`APM Agent: ${apm.isStarted() ? 'Connected' : 'Disconnected'}`);
    console.log(`APM Server: ${process.env.ELASTIC_APM_SERVER_URL || 'http://localhost:8200'}`);
    console.log('\n📋 Available commands:');
    console.log(`  - Web interface: ${host}:${port}`);
    console.log(`  - API base: ${host}:${port}/api/`);
    console.log('  - POST /api/toggle-regular - Toggle 10s interval transactions');
    console.log('  - POST /api/toggle-random - Toggle random interval transactions');
    console.log('  - POST /api/generate-manual - Generate single transaction');
    console.log('  - GET /api/status - Check current status');
    
    // Auto-start regular transactions
    console.log('\n🚀 Auto-starting regular transactions...');
    startRegularTransactions();
  });
  
  // Graceful shutdown
  process.on('SIGTERM', () => {
    console.log('\n🛑 Shutting down...');
    stopRegularTransactions();
    stopRandomTransactions();
    process.exit(0);
  });
  
  process.on('SIGINT', () => {
    console.log('\n🛑 Shutting down...');
    stopRegularTransactions();
    stopRandomTransactions();
    process.exit(0);
  });
  ```
</details>

## Step 3
#### In [Observability -> Application] page will have the client server that have dashboard to see what transaction error or what happen in Client Application

# Alert
## Step 4
#### In [Obervability -> Alert] pages will be nothing at first, because there is no rule to be alert, click Manage Rule.

---
<details>
  <summary>Create rules not allowed?</summary>
  
  ##### Create rules won't allowed if Kibana didn't have encryption xpack, so you will have to generate them first.
  ```bash
  /usr/share/kibana/bin/kibana-encryption-keys generate
  ```
  
  ##### Output should be like this
  ```bash
  ## Kibana Encryption Key Generation Utility
  
  The 'generate' command guides you through the process of setting encryption keys for:
  
  xpack.encryptedSavedObjects.encryptionKey
      Used to encrypt stored objects such as dashboards and visualizations
      https://www.elastic.co/guide/en/kibana/current/xpack-security-secure-saved-objects.html#xpack-security-secure-saved-objects
  
  xpack.reporting.encryptionKey
      Used to encrypt saved reports
      https://www.elastic.co/guide/en/kibana/current/reporting-settings-kb.html#general-reporting-settings
  
  xpack.security.encryptionKey
      Used to encrypt session information
      https://www.elastic.co/guide/en/kibana/current/security-settings-kb.html#security-session-and-cookie-settings
  
  
  Already defined settings are ignored and can be regenerated using the --force flag.  Check the documentation links for instructions on how to rotate encryption keys.
  Definitions should be set in the kibana.yml used configure Kibana.
  
  Settings:
  xpack.encryptedSavedObjects.encryptionKey: c89682cb740d28a00ecbbf31928d48db
  xpack.reporting.encryptionKey: 979d5d60a3ea570fb1b62395464def93
  xpack.security.encryptionKey: 2b73583647548cff71818d0fe7369d96
  ```
  
  ##### Put the 3 final lines in kibana.yml then restart Kibana Service.
</details>

## Step 5
#### Select what rule will be create, this time we will choose APM Error Alerting, Then basic Setup.



###### From this setup, Alerting will work when there is error in past 5 minute no matter how many error happen.
