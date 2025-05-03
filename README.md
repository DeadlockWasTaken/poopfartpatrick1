# poopfartpatrick1
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>FreqBox Prototype</title>
  <script src="https://cdn.jsdelivr.net/npm/tone@14.8.39/build/Tone.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #111;
      color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
      padding: 20px;
    }
    
    h1 {
      margin: 20px 0;
    }
    
    .keyboard-container {
      margin: 30px 0;
      position: relative;
      width: 100%;
      max-width: 700px;
      height: 210px;
    }
    
    .keyboard {
      display: flex;
      width: 100%;
      height: 200px;
      position: relative;
      border: 1px solid #333;
      border-radius: 5px;
      overflow: visible;
    }
    
    .key {
      flex: 1;
      background: white;
      border-right: 1px solid #333;
      border-radius: 0 0 5px 5px;
      position: relative;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-end;
      padding-bottom: 15px;
      z-index: 1;
      cursor: pointer;
      color: black;
    }
    
    .key:last-child {
      border-right: none;
    }
    
    .key.black {
      position: absolute;
      background: black;
      width: 8%;
      height: 60%;
      z-index: 2;
      border-radius: 0 0 3px 3px;
      border: 1px solid #000;
      color: white;
    }
    
    .note-name {
      font-weight: bold;
      font-size: 16px;
      margin-bottom: 8px;
    }
    
    .key-bind {
      font-size: 14px;
      color: #666;
    }
    
    .key.black .key-bind {
      color: #aaa;
    }
    
    .key.active {
      background-color: #ccc;
    }
    
    .key.black.active {
      background-color: #333;
    }
    
    .controls {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 20px;
      margin: 20px;
      padding: 20px;
      background: #222;
      border-radius: 10px;
      max-width: 1200px;
      width: 100%;
    }
    
    .control-group {
      background: #333;
      padding: 15px;
      border-radius: 8px;
    }
    
    .ctrl-group h3 {
      margin: 0 0 10px 0;
      color: #0af;
    }
    
    label {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      font-size: 14px;
      margin: 5px 0;
    }
    
    input[type="range"] {
      width: 100%;
      margin: 5px 0;
    }
    
    select {
      padding: 5px;
      background: #444;
      color: white;
      border: 1px solid #555;
      border-radius: 4px;
      margin-bottom: 5px;
    }

    .transpose-ctrl {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
      margin: 15px 0;
      width: 100%;
      max-width: 300px;
    }
    
    .transpose-btn {
      width: 40px;
      height: 40px;
      font-size: 20px;
      background: #444;
      color: white;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    .transpose-btn:hover {
      background: #555;
    }
    
    .transpose-display {
      font-size: 18px;
      background: #333;
      padding: 8px 15px;
      border-radius: 5px;
      min-width: 80px;
      text-align: center;
    }

   
    .preset-container {
      display: flex;
      align-items: center;
      gap: 10px;
      background: #333;
      padding: 15px;
      border-radius: 8px;
      max-width: 700px;
      width: 100%;
      margin-bottom: 20px;
    }
    
    .preset-label {
      font-weight: bold;
      white-space: nowrap;
    }
    
    #preset-select {
      flex-grow: 1;
    }
    
    .preset-btn {
      background: #0af;
      color: white;
      border: none;
      border-radius: 4px;
      padding: 8px 15px;
      cursor: pointer;
      font-weight: bold;
    }
    
    .preset-btn:hover {
      background: #09e;
    }
    
    #preset-name-input {
      flex-grow: 1;
      padding: 8px;
      background: #444;
      color: white;
      border: 1px solid #555;
      border-radius: 4px;
    }

    @media (max-width: 768px) {
      .controls {
        grid-template-columns: 1fr;
      }
      
      .preset-container {
        flex-direction: column;
        align-items: stretch;
      }
    }
  </style>
</head>
<body>
  <h1>FreqBox Prototype</h1>
  
  <div class="preset-container">
    <div class="preset-label">Presets:</div>
    <select id="preset-select">
      <option value="default">Default</option>
      <option value="bass">Deep Bass</option>
      <option value="strings">Strings</option>
      <option value="synth-lead">Synth Lead</option>
    </select>
    <input type="text" id="preset-name-input" placeholder="New preset name">
    <button id="save-preset-btn" class="preset-btn">Save</button>
    <button id="delete-preset-btn" class="preset-btn">Delete</button>
  </div>
  
  <div class="transpose-control">
    <button class="transpose-btn" id="transpose-down">-</button>
    <div class="transpose-display">
      Transpose: <span id="transpose-value">0</span>
    </div>
    <button class="transpose-btn" id="transpose-up">+</button>
  </div>
  
  <div class="keyboard-container">
    <div id="keyboard" class="keyboard"></div>
  </div>
  
  <div class="controls">
    <div class="control-group">
      <h3>ADSR Envelope</h3>
      <label>Attack <input type="range" id="attack" min="0" max="2" step="0.01" value="0.1" /></label>
      <label>Decay <input type="range" id="decay" min="0" max="2" step="0.01" value="0.2" /></label>
      <label>Sustain <input type="range" id="sustain" min="0" max="1" step="0.01" value="0.5" /></label>
      <label>Release <input type="range" id="release" min="0" max="2" step="0.01" value="0.5" /></label>
    </div>

    <div class="control-group">
      <h3>Oscillator</h3>
      <label>
        Waveform
        <select id="waveform">
          <option value="sine">Sine</option>
          <option value="square">Square</option>
          <option value="triangle">Triangle</option>
          <option value="sawtooth">Sawtooth</option>
        </select>
      </label>
      <label>Volume <input type="range" id="volume" min="-60" max="0" step="1" value="-12" /></label>
    </div>

    <div class="control-group">
      <h3>Filter</h3>
      <label>
        Type
        <select id="filterType">
          <option value="none">None</option>
          <option value="lowpass">Low Pass</option>
          <option value="highpass">High Pass</option>
        </select>
      </label>
      <label>Cutoff <input type="range" id="filterCutoff" min="20" max="20000" step="1" value="5000" /></label>
      <label>Resonance <input type="range" id="filterRes" min="0" max="20" step="0.1" value="1" /></label>
    </div>

    <div class="control-group">
      <h3>LFO</h3>
      <label>Rate <input type="range" id="lfoRate" min="0.1" max="20" step="0.1" value="5" /></label>
      <label>Depth <input type="range" id="lfoDepth" min="0" max="100" step="1" value="0" /></label>
      <label>
        Target
        <select id="lfoTarget">
          <option value="pitch">Pitch</option>
          <option value="filter">Filter</option>
          <option value="volume">Volume</option>
        </select>
      </label>
    </div>

    <div class="control-group">
      <h3>FM Modulation</h3>
      <label>
        Type
        <select id="modType">
          <option value="sine">Sine</option>
          <option value="square">Square</option>
          <option value="triangle">Triangle</option>
        </select>
      </label>
      <label>Ratio <input type="range" id="modRatio" min="0.5" max="8" step="0.5" value="1" /></label>
      <label>Depth <input type="range" id="modDepth" min="0" max="1000" step="1" value="0" /></label>
    </div>

    <div class="control-group">
      <h3>Effects</h3>
      <label>Chorus <input type="range" id="chorusAmount" min="0" max="1" step="0.01" value="0" /></label>
      <label>Delay <input type="range" id="delayAmount" min="0" max="1" step="0.01" value="0" /></label>
      <label>Reverb <input type="range" id="reverbAmount" min="0" max="1" step="0.01" value="0" /></label>
    </div>
  </div>

  <script>
    
    let synth, filter, chorus, delay, reverb, modulator, modGain;
    
    let lfoOsc, lfoGain;
    let lfoTarget = "pitch";
    
    const notes = [
      { note: 'C4', key: 'a', isBlack: false, position: 0 },
      { note: 'C#4', key: 'w', isBlack: true, position: 0.5 },
      { note: 'D4', key: 's', isBlack: false, position: 1 },
      { note: 'D#4', key: 'e', isBlack: true, position: 1.5 },
      { note: 'E4', key: 'd', isBlack: false, position: 2 },
      { note: 'F4', key: 'f', isBlack: false, position: 3 },
      { note: 'F#4', key: 't', isBlack: true, position: 3.5 },
      { note: 'G4', key: 'g', isBlack: false, position: 4 },
      { note: 'G#4', key: 'y', isBlack: true, position: 4.5 },
      { note: 'A4', key: 'h', isBlack: false, position: 5 },
      { note: 'A#4', key: 'u', isBlack: true, position: 5.5 },
      { note: 'B4', key: 'j', isBlack: false, position: 6 },
      { note: 'C5', key: 'k', isBlack: false, position: 7 }
    ];
    
    let activeTranspose = 0;
    const transposeDisplay = document.getElementById('transpose-value');
    const transposeUpBtn = document.getElementById('transpose-up');
    const transposeDownBtn = document.getElementById('transpose-down');
    
    const activeKeys = new Set();
    
    const defaultPresets = {
      "default": {
        attack: 0.1,
        decay: 0.2,
        sustain: 0.5,
        release: 0.5,
        waveform: "sine",
        volume: -12,
        filterType: "none",
        filterCutoff: 5000,
        filterRes: 1,
        lfoRate: 5,
        lfoDepth: 0,
        lfoTarget: "pitch",
        modType: "sine",
        modRatio: 1,
        modDepth: 0,
        chorusAmount: 0,
        delayAmount: 0,
        reverbAmount: 0
      },
      "deep-bass": {
        attack: 0.1,
        decay: 0.5,
        sustain: 0.8,
        release: 1.5,
        waveform: "sawtooth",
        volume: -6,
        filterType: "lowpass",
        filterCutoff: 500,
        filterRes: 5,
        lfoRate: 0.5,
        lfoDepth: 10,
        lfoTarget: "filter",
        modType: "sine",
        modRatio: 2,
        modDepth: 50,
        chorusAmount: 0.2,
        delayAmount: 0,
        reverbAmount: 0.1
      },
      "strings": {
        attack: 0.5,
        decay: 0.8,
        sustain: 0.7,
        release: 1.2,
        waveform: "triangle",
        volume: -10,
        filterType: "lowpass",
        filterCutoff: 3000,
        filterRes: 1,
        lfoRate: 6,
        lfoDepth: 15,
        lfoTarget: "pitch",
        modType: "sine",
        modRatio: 1.5,
        modDepth: 20,
        chorusAmount: 0.8,
        delayAmount: 0.1,
        reverbAmount: 0.6
      },
      "synth-lead": {
        attack: 0.01,
        decay: 0.3,
        sustain: 0.6,
        release: 0.2,
        waveform: "square",
        volume: -8,
        filterType: "lowpass",
        filterCutoff: 8000,
        filterRes: 8,
        lfoRate: 7,
        lfoDepth: 30,
        lfoTarget: "filter",
        modType: "triangle",
        modRatio: 4,
        modDepth: 200,
        chorusAmount: 0.3,
        delayAmount: 0.4,
        reverbAmount: 0.1
      }
    };

    let userPresets = {};

    function loadUserPresets() {
      const savedPresets = localStorage.getItem('freqboxPresets');
      if (savedPresets) {
        try {
          userPresets = JSON.parse(savedPresets);
        } catch (e) {
          console.error("Failed to load saved presets", e);
          userPresets = {};
        }
      }
    }

    function populatePresetDropdown() {
      const presetSelect = document.getElementById('preset-select');
      presetSelect.innerHTML = '';

      const defaultOptions = [
        { id: 'default', name: 'Default' },
        { id: 'deep-bass', name: 'Deep Bass' },
        { id: 'strings', name: 'Strings' },
        { id: 'synth-lead', name: 'Synth Lead' }
      ];

      for (const preset of defaultOptions) {
        const option = document.createElement('option');
        option.value = preset.id;
        option.textContent = preset.name;
        presetSelect.appendChild(option);
      }

      if (Object.keys(userPresets).length > 0) {
        const separator = document.createElement('option');
        separator.disabled = true;
        separator.textContent = '────────────────';
        presetSelect.appendChild(separator);
      }

      for (const [id, preset] of Object.entries(userPresets)) {
        const option = document.createElement('option');
        option.value = id;
        option.textContent = preset.name || id;
        option.classList.add('user-preset');
        presetSelect.appendChild(option);
      }

      const customOption = document.createElement('option');
      customOption.value = "custom";
      customOption.textContent = "Custom";
      presetSelect.appendChild(customOption);
    }

    function getCurrentSettings() {
      return {
        name: document.getElementById('preset-name-input').value.trim() || "Unnamed Preset",
        attack: parseFloat(document.getElementById("attack").value),
        decay: parseFloat(document.getElementById("decay").value),
        sustain: parseFloat(document.getElementById("sustain").value),
        release: parseFloat(document.getElementById("release").value),
        waveform: document.getElementById("waveform").value,
        volume: parseFloat(document.getElementById("volume").value),
        filterType: document.getElementById("filterType").value,
        filterCutoff: parseFloat(document.getElementById("filterCutoff").value),
        filterRes: parseFloat(document.getElementById("filterRes").value),
        lfoRate: parseFloat(document.getElementById("lfoRate").value),
        lfoDepth: parseFloat(document.getElementById("lfoDepth").value),
        lfoTarget: document.getElementById("lfoTarget").value,
        modType: document.getElementById("modType").value,
        modRatio: parseFloat(document.getElementById("modRatio").value),
        modDepth: parseFloat(document.getElementById("modDepth").value),
        chorusAmount: parseFloat(document.getElementById("chorusAmount").value),
        delayAmount: parseFloat(document.getElementById("delayAmount").value),
        reverbAmount: parseFloat(document.getElementById("reverbAmount").value)
      };
    }

    function loadPreset(presetId) {
      let preset;

      if (!presetId || presetId === "custom") {
        return;
      }

      if (defaultPresets[presetId]) {
        preset = defaultPresets[presetId];
      } else if (userPresets[presetId]) {
        preset = userPresets[presetId];
      } else {
        console.error("Preset not found:", presetId);
        return;
      }

      console.log("Loading preset:", presetId, preset);

      for (const [key, value] of Object.entries(preset)) {
        if (key === 'name') continue;

        const control = document.getElementById(key);
        if (control) {
          control.value = value;
        } else {
          console.warn(`Control not found for preset property: ${key}`);
        }
      }

      updateControls("preset");
    }

    function savePreset() {
      const settings = getCurrentSettings();
      const name = settings.name;

      if (!name) {
        alert("Please enter a name for your preset");
        return;
      }

      const presetId = name.toLowerCase().replace(/\s+/g, '-');

      userPresets[presetId] = settings;

      localStorage.setItem('freqboxPresets', JSON.stringify(userPresets));

      populatePresetDropdown();
      document.getElementById('preset-select').value = presetId;

      alert(`Preset "${name}" saved successfully!`);
    }

    function deletePreset() {
      const presetSelect = document.getElementById('preset-select');
      const selectedId = presetSelect.value;

      if (defaultPresets[selectedId]) {
        alert("Cannot delete default presets");
        return;
      }

      if (selectedId === "custom") {
        alert("Cannot delete the custom state");
        return;
      }

      if (userPresets[selectedId]) {
        const name = userPresets[selectedId].name || selectedId;

        if (confirm(`Are you sure you want to delete the preset "${name}"?`)) {
          delete userPresets[selectedId];
          localStorage.setItem('freqboxPresets', JSON.stringify(userPresets));

          populatePresetDropdown();
          presetSelect.value = 'Default';
          loadPreset('Default');

          alert(`Preset "${name}" deleted.`);
        }
      }
    }

    function setupPresetControls() {
      const presetSelect = document.getElementById('preset-select');
      const saveBtn = document.getElementById('save-preset-btn');
      const deleteBtn = document.getElementById('delete-preset-btn');

      presetSelect.addEventListener('change', () => {
        const selectedValue = presetSelect.value;
        if (selectedValue !== "custom") {
          loadPreset(selectedValue);
        }
      });

      saveBtn.addEventListener('click', savePreset);

      deleteBtn.addEventListener('click', deletePreset);
    }

    function updateControls(triggeredBy = "UI") {
      if (!synth) return;
      const attack = parseFloat(document.getElementById("attack").value);
      const decay = parseFloat(document.getElementById("decay").value);
      const sustain = parseFloat(document.getElementById("sustain").value);
      const release = parseFloat(document.getElementById("release").value);
      synth.set({
        envelope: { attack, decay, sustain, release }
      });
      const waveform = document.getElementById("waveform").value;
      const volume = parseFloat(document.getElementById("volume").value);
      synth.set({
        oscillator: { type: waveform },
        volume: volume
      });
      const filterType = document.getElementById("filterType").value;
      const cutoff = parseFloat(document.getElementById("filterCutoff").value);
      const resonance = parseFloat(document.getElementById("filterRes").value);
      if (filter) {
        if (filterType === "none") {
          filter.frequency.value = 20000;
          filter.Q.value = 0.1;
        } else {
          filter.type = filterType;
          filter.frequency.value = cutoff;
          filter.Q.value = resonance;
        }
      }
      if (lfoOsc && lfoGain) {
        const lfoRate = parseFloat(document.getElementById("lfoRate").value);
        const lfoDepth = parseFloat(document.getElementById("lfoDepth").value);
        const newLfoTarget = document.getElementById("lfoTarget").value;
        lfoOsc.frequency.value = lfoRate;
        let scaledDepth = 0;
        switch (lfoTarget) {
          case "pitch":
            scaledDepth = lfoDepth; 
            break;
          case "filter":
            scaledDepth = lfoDepth * 40; 
            break;
          case "volume":
            scaledDepth = lfoDepth / 10; 
            break;
        }
        lfoGain.gain.value = scaledDepth;
        if (newLfoTarget !== lfoTarget) {
          setLfoTarget(newLfoTarget);
        }
      }
      if (modulator && modGain) {
        const modType = document.getElementById("modType").value;
        const modDepth = parseFloat(document.getElementById("modDepth").value);
        const modRatio = parseFloat(document.getElementById("modRatio").value);
        modulator.type = modType;
        modGain.gain.value = modDepth;
        activeKeys.forEach(key => {
          const match = notes.find(n => n.key === key.toLowerCase());
          if (match) {
            const baseFreq = Tone.Frequency(getTransposedNote(match.note)).toFrequency();
            modulator.frequency.value = baseFreq * modRatio;
          }
        });
      }
      if (chorus && delay && reverb) {
        chorus.wet.value = parseFloat(document.getElementById("chorusAmount").value);
        delay.wet.value = parseFloat(document.getElementById("delayAmount").value);
        reverb.wet.value = parseFloat(document.getElementById("reverbAmount").value);
      }
      if (triggeredBy !== "preset") {
        const presetSelect = document.getElementById("preset-select");
        if (presetSelect) {
          presetSelect.value = "custom";
        }
      }
    }
    function initAudio() {
      if (synth) return Promise.resolve();
      return Tone.start().then(() => {
        chorus = new Tone.Chorus(4, 2.5, 0.5).start();
        delay = new Tone.FeedbackDelay("8n", 0.5);
        reverb = new Tone.Reverb(3);
        filter = new Tone.Filter({
          type: "lowpass",
          frequency: 5000,
          rolloff: -24
        });
        const limiter = new Tone.Limiter(-6);
        lfoOsc = new Tone.Oscillator({
          type: "sine",
          frequency: 5
        }).start();
        
        lfoGain = new Tone.Gain(0);
        lfoOsc.connect(lfoGain);
        

        modulator = new Tone.Oscillator({
          type: "sine",
          frequency: 0
        }).start();
        
        modGain = new Tone.Gain(0);
        modulator.connect(modGain);

        synth = new Tone.PolySynth(Tone.Synth, {
          oscillator: { type: "sine" },
          envelope: {
            attack: 0.1,
            decay: 0.2,
            sustain: 0.5,
            release: 0.5
          },
          volume: -12,
          portamento: 0
        }).chain(filter, chorus, delay, reverb, limiter, Tone.Destination);

        modGain.connect(synth.frequency);
        
        setLfoTarget("pitch");
        
        updateControls();
      });
    }
    
    function setLfoTarget(target) {
      if (!lfoGain || !lfoOsc) return;
      
      lfoGain.disconnect();
      
      switch (target) {
        case "pitch":
          lfoGain.connect(synth.frequency);
          break;
        case "filter":
          lfoGain.connect(filter.frequency);
          break;
        case "volume":
          lfoGain.connect(synth.volume);
          break;
      }
      
      lfoTarget = target;
    }
    
    function createKeyboard() {
      const keyboard = document.getElementById('keyboard');
      keyboard.innerHTML = '';
      
      const whiteKeys = notes.filter(n => !n.isBlack);
      whiteKeys.forEach(noteInfo => {
        const keyDiv = document.createElement('div');
        keyDiv.className = 'key';
        keyDiv.dataset.note = noteInfo.note;
        
        const noteName = document.createElement('div');
        noteName.className = 'note-name';
        noteName.textContent = noteInfo.note.replace(/[0-9]/, '');
        
        const keyBind = document.createElement('div');
        keyBind.className = 'key-bind';
        keyBind.textContent = noteInfo.key.toUpperCase();
        
        keyDiv.appendChild(noteName);
        keyDiv.appendChild(keyBind);
        keyboard.appendChild(keyDiv);
        
        keyDiv.addEventListener('mousedown', handleKeyDown);
        keyDiv.addEventListener('mouseup', handleKeyUp);
        keyDiv.addEventListener('mouseleave', handleKeyLeave);
        keyDiv.addEventListener('touchstart', handleKeyDown);
        keyDiv.addEventListener('touchend', handleKeyUp);
      });
      
      const keyboardWidth = keyboard.clientWidth;
      const whiteKeyWidth = keyboardWidth / whiteKeys.length;
      
      notes.filter(n => n.isBlack).forEach(noteInfo => {
        const keyDiv = document.createElement('div');
        keyDiv.className = 'key black';
        keyDiv.dataset.note = noteInfo.note;
        
        keyDiv.style.left = `${noteInfo.position * whiteKeyWidth + whiteKeyWidth/6}px`;
        
        const noteName = document.createElement('div');
        noteName.className = 'note-name';
        noteName.textContent = noteInfo.note.replace(/[0-9]/, '');
        
        const keyBind = document.createElement('div');
        keyBind.className = 'key-bind';
        keyBind.textContent = noteInfo.key.toUpperCase();
        
        keyDiv.appendChild(noteName);
        keyDiv.appendChild(keyBind);
        keyboard.appendChild(keyDiv);
        
        keyDiv.addEventListener('mousedown', handleKeyDown);
        keyDiv.addEventListener('mouseup', handleKeyUp);
        keyDiv.addEventListener('mouseleave', handleKeyLeave);
        keyDiv.addEventListener('touchstart', handleKeyDown);
        keyDiv.addEventListener('touchend', handleKeyUp);
      });
    }
    
    async function handleKeyDown(event) {
      event.preventDefault();
      
      await initAudio();
      
      const note = this.dataset.note;
      if (!note) return;
      
      playNote(note);
      this.classList.add('active');
    }
    
    function handleKeyUp(event) {
      event.preventDefault();
      
      const note = this.dataset.note;
      if (!note) return;
      
      stopNote(note);
      this.classList.remove('active');
    }
    
    function handleKeyLeave(event) {
      if (event.buttons === 1) { 
        const note = this.dataset.note;
        if (!note) return;
        
        stopNote(note);
        this.classList.remove('active');
      }
    }
    
    function getTransposedNote(note) {
      return Tone.Frequency(note).transpose(activeTranspose).toNote();
    }
    
    function playNote(note) {
      if (!synth) return;
      
      const transposedNote = getTransposedNote(note);
      if (modulator) {
        const baseFreq = Tone.Frequency(transposedNote).toFrequency();
        const ratio = parseFloat(document.getElementById("modRatio").value || 1);
        modulator.frequency.value = baseFreq * ratio;
      }
      synth.triggerAttack(transposedNote);
    }
    
    function stopNote(note) {
      if (!synth) return;
      
      const transposedNote = getTransposedNote(note);
      synth.triggerRelease(transposedNote);
    }
    
    function setupTransposeControls() {
      transposeUpBtn.addEventListener('click', () => {
        if (activeTranspose < 12) {
          activeTranspose++;
          updateTransposeDisplay();
        }
      });
      
      transposeDownBtn.addEventListener('click', () => {
        if (activeTranspose > -12) {
          activeTranspose--;
          updateTransposeDisplay();
        }
      });
      
      updateTransposeDisplay();
    }
    
    function updateTransposeDisplay() {
      transposeDisplay.textContent = activeTranspose;
    }
    
    function updateControls(triggeredBy = "UI") {
      if (!synth) return;
      
      const attack = parseFloat(document.getElementById("attack").value);
      const decay = parseFloat(document.getElementById("decay").value);
      const sustain = parseFloat(document.getElementById("sustain").value);
      const release = parseFloat(document.getElementById("release").value);
      
      synth.set({
        envelope: { attack, decay, sustain, release }
      });

      const waveform = document.getElementById("waveform").value;
      const volume = parseFloat(document.getElementById("volume").value);
      
      synth.set({
        oscillator: { type: waveform },
        volume: volume
      });

      const filterType = document.getElementById("filterType").value;
      const cutoff = parseFloat(document.getElementById("filterCutoff").value);
      const resonance = parseFloat(document.getElementById("filterRes").value);
      
      if (filter) {
        if (filterType === "none") {
          filter.frequency.value = 20000;
          filter.Q.value = 0.1;
        } else {
          filter.type = filterType;
          filter.frequency.value = cutoff;
          filter.Q.value = resonance;
        }
      }

      if (lfoOsc && lfoGain) {
        const lfoRate = parseFloat(document.getElementById("lfoRate").value);
        const lfoDepth = parseFloat(document.getElementById("lfoDepth").value);
        const newLfoTarget = document.getElementById("lfoTarget").value;
        
        lfoOsc.frequency.value = lfoRate;
        
        let scaledDepth = 0;
                switch (lfoTarget) {
                  case "pitch":
                    scaledDepth = lfoDepth; 
                    break;
                  case "filter":
                    scaledDepth = lfoDepth * 40;
                    break;
                  case "volume":
                    scaledDepth = lfoDepth / 10;
                    break;
                }

                lfoGain.gain.value = scaledDepth;

                if (newLfoTarget !== lfoTarget) {
                  setLfoTarget(newLfoTarget);
                }
              }

              if (modulator && modGain) {
                const modType = document.getElementById("modType").value;
                const modDepth = parseFloat(document.getElementById("modDepth").value);
                const modRatio = parseFloat(document.getElementById("modRatio").value);

                modulator.type = modType;
                modGain.gain.value = modDepth;

                activeKeys.forEach(key => {
                  const match = notes.find(n => n.key === key.toLowerCase());
                  if (match) {
                    const baseFreq = Tone.Frequency(getTransposedNote(match.note)).toFrequency();
                    modulator.frequency.value = baseFreq * modRatio;
                  }
                });
              }

              if (chorus && delay && reverb) {
                chorus.wet.value = parseFloat(document.getElementById("chorusAmount").value);
                delay.wet.value = parseFloat(document.getElementById("delayAmount").value);
                reverb.wet.value = parseFloat(document.getElementById("reverbAmount").value);
              }

              if (triggeredBy !== "preset") {
                const presetSelect = document.getElementById("preset-select");
                const customOption = Array.from(presetSelect.options).find(opt => opt.value === "custom");

                if (customOption) {
                  presetSelect.value = "custom";
                }
              }
            }

            function setupKeyboardEvents() {
              document.addEventListener("keydown", async (e) => {
                if (e.repeat) return; // prevent bugzzzzz

                const match = notes.find(n => n.key === e.key.toLowerCase());
                if (match && !activeKeys.has(e.key)) {
                  activeKeys.add(e.key);

                  await initAudio();
                  playNote(match.note);

                  const keyElement = document.querySelector(`.key[data-note="${match.note}"]`);
                  if (keyElement) {
                    keyElement.classList.add('active');
                  }
                }
              });

              document.addEventListener("keyup", (e) => {
                const match = notes.find(n => n.key === e.key.toLowerCase());
                if (match) {
                  activeKeys.delete(e.key);
                  stopNote(match.note);

                  const keyElement = document.querySelector(`.key[data-note="${match.note}"]`);
                  if (keyElement) {
                    keyElement.classList.remove('active');
                  }
                }
              });
            }

            function setupPresetControls() {
              const presetSelect = document.getElementById('preset-select');
              const saveBtn = document.getElementById('save-preset-btn');
              const deleteBtn = document.getElementById('delete-preset-btn');

              presetSelect.addEventListener('change', () => {
                loadPreset(presetSelect.value);
              });

              saveBtn.addEventListener('click', savePreset);

              deleteBtn.addEventListener('click', deletePreset);

              const customOption = document.createElement('option');
              customOption.value = "custom";
              customOption.textContent = "Custom";
              presetSelect.appendChild(customOption);
            }

            function setupControlListeners() {
              document.querySelectorAll('input, select:not(#preset-select)').forEach(control => {
                control.addEventListener('input', () => updateControls("UI"));
              });
            }

            function initApp() {
              loadUserPresets();

              createKeyboard();
              setupTransposeControls();
              setupKeyboardEvents();
              setupControlListeners();
              setupPresetControls();

              populatePresetDropdown();
              document.getElementById('preset-select').value = 'default';

              window.addEventListener('resize', createKeyboard);
            }

            document.addEventListener('DOMContentLoaded', initApp);

            if (document.readyState !== 'loading') {
              initApp();
            }
          </script>
        </body>
        </html>
