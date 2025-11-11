<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Student Notes Share — PDF Portal</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--muted:#9aa4b2;--accent:#60a5fa;--glass: rgba(255,255,255,0.03)}
    *{box-sizing:border-box;font-family:Inter,ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,'Helvetica Neue',Arial}
    body{margin:0;background:linear-gradient(180deg,#071024 0%, #081122 100%);color:#e6eef6;min-height:100vh;display:flex;align-items:flex-start;justify-content:center;padding:28px}
    .wrap{max-width:1100px;width:100%;}
    header{display:flex;align-items:center;gap:14px;margin-bottom:18px}
    header h1{margin:0;font-size:20px}
    .grid{display:grid;grid-template-columns:1fr 380px;gap:18px}
    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:18px;border-radius:12px;box-shadow:0 6px 24px rgba(2,6,23,0.6);}
    label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
    input[type=text], textarea, select{width:100%;padding:10px;border-radius:8px;background:var(--glass);border:1px solid rgba(255,255,255,0.04);color:inherit}
    textarea{min-height:120px;resize:vertical}
    .btn{display:inline-block;padding:10px 12px;border-radius:10px;background:linear-gradient(90deg,var(--accent),#34d399);color:#04201a;font-weight:600;border:none;cursor:pointer}
    .btn.secondary{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,0.04)}
    .muted{color:var(--muted);font-size:13px}
    .preview{background:rgba(255,255,255,0.02);padding:12px;border-radius:8px;min-height:220px;overflow:auto}
    .file-list{margin-top:10px}
    .share-link{word-break:break-all;background:#022;display:block;padding:10px;border-radius:8px;margin-top:8px}
    footer{margin-top:12px;color:var(--muted);font-size:13px}
    @media (max-width:980px){.grid{grid-template-columns:1fr}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <svg width="40" height="40" viewBox="0 0 24 24" fill="none"><rect width="24" height="24" rx="6" fill="#04202a"/><path d="M7 8h10M7 12h10M7 16h6" stroke="#60a5fa" stroke-width="1.5" stroke-linecap="round"/></svg>
      <div>
        <h1>Student Notes Share — PDF Portal</h1>
        <div class="muted">Upload, preview, and share your class notes PDFs instantly.</div>
      </div>
    </header>

    <div class="grid">
      <section class="card">
        <label for="title">Notes Title</label>
        <input id="title" type="text" placeholder="E.g., Power Electronics — Lecture 05" />

        <div style="display:flex;gap:10px;margin-top:10px">
          <div style="flex:1">
            <label for="subject">Subject</label>
            <input id="subject" type="text" placeholder="E.g., Power Electronics" />
          </div>
          <div style="width:140px">
            <label for="semester">Semester</label>
            <select id="semester">
              <option value="S1">S1</option>
              <option value="S2">S2</option>
              <option value="S3">S3</option>
              <option value="S4">S4</option>
              <option value="S5">S5</option>
              <option value="S6">S6</option>
              <option value="S7">S7</option>
              <option value="S8">S8</option>
            </select>
          </div>
        </div>

        <label for="tags" style="margin-top:12px">Tags (comma-separated)</label>
        <input id="tags" type="text" placeholder="e.g., lecture,exam,notes,chapter5" />

        <label for="desc" style="margin-top:12px">Description</label>
        <textarea id="desc" placeholder="Short summary of the notes...">Compact notes covering key topics and examples.</textarea>

        <div style="display:flex;gap:8px;margin-top:12px;align-items:center">
          <label class="btn" for="fileinput">📁 Upload PDF(s)</label>
          <input id="fileinput" type="file" style="display:none" accept=".pdf" multiple />
          <button id="generate" class="btn">Generate Share Link</button>
          <button id="exportJson" class="btn secondary">Export Metadata</button>
        </div>

        <div class="muted" style="margin-top:8px">Uploaded files:</div>
        <div id="fileList" class="file-list muted">No files uploaded.</div>

        <div style="margin-top:12px">
          <label class="muted">Preview</label>
          <div id="preview" class="preview muted">Upload a PDF and click 'Preview'.</div>
        </div>

        <div style="margin-top:12px;display:flex;gap:8px;flex-wrap:wrap">
          <button id="downloadSample" class="btn secondary">Download Sample Notes</button>
          <button id="clear" class="btn secondary">Clear</button>
        </div>
      </section>

      <aside class="card">
        <div>
          <div class="muted">Shareable Link</div>
          <div id="link" class="share-link">—</div>
        </div>

        <div style="margin-top:12px">
          <div class="muted">How to use</div>
          <ol style="color:var(--muted);font-size:13px;line-height:1.5;margin-top:8px;padding-left:18px">
            <li>Upload one or more PDF notes files.</li>
            <li>Fill title, subject, semester and tags.</li>
            <li>Click <b>Generate Share Link</b> to create a browser-accessible blob link.</li>
            <li>Click <b>Export Metadata</b> to save a JSON file with all info.</li>
          </ol>
        </div>

        <footer>Made with ❤️ — HTML5 · JS · CSS3</footer>
      </aside>
    </div>
  </div>

  <script>
    const fileInput = document.getElementById('fileinput');
    const fileList = document.getElementById('fileList');
    const preview = document.getElementById('preview');
    const linkDiv = document.getElementById('link');
    const generate = document.getElementById('generate');
    const exportJson = document.getElementById('exportJson');
    const title = document.getElementById('title');
    const subject = document.getElementById('subject');
    const semester = document.getElementById('semester');
    const tagsInput = document.getElementById('tags');
    const desc = document.getElementById('desc');
    const downloadSample = document.getElementById('downloadSample');
    const clearBtn = document.getElementById('clear');

    let files = [];
    let lastBlobURL = null;

    function renderFileList(){
      if(files.length===0){ fileList.textContent='No files uploaded.'; preview.textContent='Upload a PDF and click "Preview".'; return }
      fileList.innerHTML = '';
      files.forEach((f, i)=>{
        const el = document.createElement('div');
        el.style.padding='6px 0';
        el.innerHTML = `<strong>${f.name}</strong> <span class='muted' style='margin-left:8px'>(${Math.round(f.size/1024)} KB)</span> <button data-i='${i}' class='btn secondary small preview-btn' style='margin-left:8px'>Preview</button>`;
        fileList.appendChild(el);
      });
      fileList.querySelectorAll('.preview-btn').forEach(btn=>btn.addEventListener('click', (e)=>{
        const i = +btn.getAttribute('data-i');
        previewFile(files[i]);
      }));
    }

    function previewFile(file){
      preview.textContent = 'Loading preview...';
      const name = file.name.toLowerCase();
      if(name.endsWith('.pdf')){
        const url = URL.createObjectURL(file);
        preview.innerHTML = `<iframe src='${url}' style='width:100%;height:360px;border:none;border-radius:6px'></iframe>`;
      } else {
        preview.textContent = 'Preview not available for this file type.';
      }
    }

    fileInput.addEventListener('change', (e)=>{
      const list = Array.from(e.target.files);
      const onlyPdf = list.filter(f=>f.name.toLowerCase().endsWith('.pdf'));
      if(onlyPdf.length !== list.length) alert('Only PDF files are accepted.');
      files = files.concat(onlyPdf);
      renderFileList();
    });

    generate.addEventListener('click', ()=>{
      if(files.length===0){ alert('Please upload at least one PDF.'); return }
      const packageObj = {
        title: title.value,
        subject: subject.value,
        semester: semester.value,
        tags: tagsInput.value.split(',').map(t=>t.trim()).filter(Boolean),
        description: desc.value,
        createdAt: new Date().toISOString(),
        files: files.map(f=>({name:f.name,size:f.size,type:f.type}))
      };
      const metaBlob = new Blob([JSON.stringify(packageObj, null, 2)], {type:'application/json'});
      const combined = new Blob([metaBlob, files[0]], {type:'application/octet-stream'});
      if(lastBlobURL) URL.revokeObjectURL(lastBlobURL);
      lastBlobURL = URL.createObjectURL(combined);
      linkDiv.textContent = lastBlobURL;
      linkDiv.style.color='var(--accent)';
      preview.textContent = 'Share link created (temporary, works only in this browser).';
    });

    exportJson.addEventListener('click', ()=>{
      const data = {
        title: title.value,
        subject: subject.value,
        semester: semester.value,
        tags: tagsInput.value.split(',').map(t=>t.trim()).filter(Boolean),
        description: desc.value,
        createdAt: new Date().toISOString(),
        files: files.map(f=>({name:f.name,size:f.size,type:f.type}))
      };
      const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url; a.download = (title.value||'notes') + '.metadata.json';
      document.body.appendChild(a); a.click(); a.remove();
      URL.revokeObjectURL(url);
    });

    downloadSample.addEventListener('click', ()=>{
      const sampleHtml = `<!doctype html>
<html><head><meta charset="utf-8"><title>Sample Notes</title></head>
<body><h1>Sample Student Notes</h1><p>This is a placeholder for your notes content.</p></body></html>`;
      const blob = new Blob([sampleHtml], {type:'text/html'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href=url; a.download='sample-notes.html';
      document.body.appendChild(a); a.click(); a.remove();
      URL.revokeObjectURL(url);
    });

    clearBtn.addEventListener('click', ()=>{
      files = [];
      fileInput.value='';
      renderFileList();
      linkDiv.textContent='—';
      preview.textContent='Upload a PDF and click "Preview".';
      title.value=''; subject.value=''; semester.value='S1'; tagsInput.value=''; desc.value='';
    });

    renderFileList();
  </script>
</body>
</html>
