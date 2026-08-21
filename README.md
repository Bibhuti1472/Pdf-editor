# Pdf-editor
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PDF Compressor & Watermark Editor</title>
    
    <!-- PDF.js for reading the existing PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <!-- jsPDF for building the new compressed PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        :root { 
            --primary: #4f46e5; 
            --primary-hover: #4338ca;
            --bg: #f3f4f6; 
            --text: #1f2937; 
            --text-light: #6b7280;
            --card: #ffffff;
            --border: #e5e7eb;
        }
        
        body { 
            font-family: 'Inter', system-ui, sans-serif; 
            background: linear-gradient(135deg, #f3f4f6 0%, #e5e7eb 100%);
            color: var(--text); 
            margin: 0; 
            padding: 40px 20px; 
            min-height: 100vh;
        }
        
        .container { 
            max-width: 850px; 
            margin: 0 auto; 
            background: var(--card); 
            padding: 40px; 
            border-radius: 16px; 
            box-shadow: 0 10px 25px rgba(0,0,0,0.05), 0 4px 6px rgba(0,0,0,0.02); 
        }
        
        h1 { 
            text-align: center; 
            color: var(--primary); 
            margin-top: 0;
            margin-bottom: 35px; 
            font-weight: 700;
            font-size: 2.2rem;
            letter-spacing: -0.02em;
        }
        
        .section { 
            margin-bottom: 30px; 
            padding: 25px; 
            border: 1px solid var(--border); 
            border-radius: 12px; 
            background: #fafafa;
            transition: border-color 0.3s ease;
        }
        
        .section:hover {
            border-color: #d1d5db;
        }
        
        .section-title { 
            font-weight: 600; 
            margin-bottom: 20px; 
            font-size: 1.2rem; 
            color: #111827; 
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .section-title::before {
            content: '';
            display: block;
            width: 4px;
            height: 20px;
            background: var(--primary);
            border-radius: 4px;
        }
        
        label { 
            display: block; 
            margin-bottom: 8px; 
            font-weight: 500; 
            font-size: 0.95rem; 
            color: #374151;
        }
        
        input[type="file"], input[type="text"], input[type="number"], input[type="color"], select { 
            width: 100%; 
            padding: 12px 16px; 
            margin-bottom: 20px; 
            border: 1px solid var(--border); 
            border-radius: 8px; 
            box-sizing: border-box; 
            font-family: inherit;
            font-size: 0.95rem;
            transition: all 0.2s ease;
            background: #fff;
        }
        
        input[type="file"]::file-selector-button {
            padding: 8px 16px;
            border-radius: 6px;
            border: none;
            background: var(--primary);
            color: white;
            font-weight: 500;
            cursor: pointer;
            margin-right: 12px;
            transition: background 0.2s;
        }
        
        input[type="file"]::file-selector-button:hover {
            background: var(--primary-hover);
        }

        input:focus, select:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.15);
        }
        
        input[type="range"] { 
            width: 100%; 
            margin-bottom: 20px; 
            accent-color: var(--primary);
        }
        
        .row { display: flex; gap: 20px; flex-wrap: wrap; }
        .col { flex: 1; min-width: 200px; }
        
        .hint {
            font-size: 0.85rem; 
            color: var(--text-light);
            margin-top: -12px;
            margin-bottom: 15px;
            display: block;
        }
        
        button { 
            background-color: var(--primary); 
            color: white; 
            border: none; 
            padding: 16px 24px; 
            width: 100%; 
            font-size: 1.15rem; 
            font-weight: 600; 
            border-radius: 10px; 
            cursor: pointer; 
            transition: all 0.3s ease; 
            box-shadow: 0 4px 6px rgba(79, 70, 229, 0.2);
        }
        
        button:hover { 
            background-color: var(--primary-hover); 
            transform: translateY(-1px);
            box-shadow: 0 6px 10px rgba(79, 70, 229, 0.3);
        }
        
        button:active {
            transform: translateY(1px);
        }
        
        button:disabled { 
            background-color: #9ca3af; 
            cursor: not-allowed; 
            box-shadow: none;
            transform: none;
        }
        
        #status { 
            text-align: center; 
            margin-top: 20px; 
            font-weight: 600; 
            color: var(--primary); 
            font-size: 1.1rem; 
        }
        
        .progress-bar { 
            width: 100%; 
            background: #e5e7eb; 
            border-radius: 8px; 
            overflow: hidden; 
            display: none; 
            margin-top: 15px; 
            height: 10px;
        }
        
        .progress-fill { 
            height: 100%; 
            background: linear-gradient(90deg, #4f46e5, #818cf8); 
            width: 0%; 
            transition: width 0.3s ease; 
            border-radius: 8px;
        }

        .footer {
            text-align: center;
            margin-top: 30px;
            font-size: 0.95rem;
            color: var(--text-light);
            font-weight: 500;
        }
        
        .footer span {
            color: var(--primary);
            font-weight: 700;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>PDF Compressor & Editor</h1>
    
    <!-- 1. Upload & Compress Section -->
    <div class="section">
        <div class="section-title">1. Upload PDF & Set Compression</div>
        <label>Select PDF File (Up to 100MB)</label>
        <input type="file" id="pdfInput" accept="application/pdf" required>
        
        <label>Compression Level</label>
        <select id="compressionLevel">
            <option value="0.9">Low Compression (Best Quality)</option>
            <option value="0.6" selected>Medium Compression (Balanced - Recommended)</option>
            <option value="0.3">High Compression (Smallest Size - May reduce clarity)</option>
        </select>
        <span class="hint">* Pages are rendered at 2x resolution to maintain maximum text clarity.</span>
    </div>

    <!-- 2. Image Watermark Section -->
    <div class="section">
        <div class="section-title">2. Image Watermark (Optional)</div>
        <label>Upload Watermark Image (JPEG/PNG)</label>
        <input type="file" id="watermarkImage" accept="image/*">
        
        <div class="row">
            <div class="col">
                <label>Watermark Opacity: <span id="opacityVal">50</span>%</label>
                <input type="range" id="watermarkOpacity" min="10" max="100" value="50" oninput="document.getElementById('opacityVal').innerText = this.value">
            </div>
            <div class="col">
                <label>Watermark Size: <span id="sizeVal">40</span>%</label>
                <input type="range" id="watermarkImgSize" min="10" max="100" value="40" oninput="document.getElementById('sizeVal').innerText = this.value">
            </div>
        </div>
    </div>

    <!-- 3. Text Watermark Section -->
    <div class="section">
        <div class="section-title">3. Text Watermark (Optional)</div>
        <div class="row">
            <div class="col">
                <label>Watermark Text</label>
                <input type="text" id="watermarkText" placeholder="e.g. CONFIDENTIAL">
            </div>
            <div class="col">
                <label>Font Size</label>
                <input type="number" id="watermarkFontSize" value="40" min="10" max="200">
            </div>
        </div>
        <div class="row">
            <div class="col">
                <label>Color</label>
                <input type="color" id="watermarkColor" value="#ff0000">
            </div>
            <div class="col">
                <label>Angle</label>
                <select id="watermarkAngle">
                    <option value="45">45° Diagonal</option>
                    <option value="0">0° Horizontal (Center)</option>
                </select>
            </div>
        </div>
    </div>

    <!-- 4. Page Text Section -->
    <div class="section">
        <div class="section-title">4. Add Text to Specific Pages (Optional)</div>
        <div class="row">
            <div class="col">
                <label>Text to add (Bottom center)</label>
                <input type="text" id="pageText" placeholder="e.g. Scanned by Me">
            </div>
            <div class="col">
                <label>Page Numbers</label>
                <input type="text" id="pageNumbers" placeholder="e.g. 1, 3, 5 (Leave empty for all)">
            </div>
        </div>
    </div>

    <button id="processBtn" onclick="processPDF()">Compress & Download PDF</button>
    <div id="status"></div>
    <div class="progress-bar" id="progressBar"><div class="progress-fill" id="progressFill"></div></div>
    
    <div class="footer">Made by <span>Bibek</span></div>
</div>

<script>
    // Setup PDF.js worker
    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';
    const { jsPDF } = window.jspdf;

    const hexToRgb = (hex) => {
        const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
        return result ? { r: parseInt(result[1], 16), g: parseInt(result[2], 16), b: parseInt(result[3], 16) } : { r:0, g:0, b:0 };
    };

    const readFileAsDataURL = (file) => new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => resolve(e.target.result);
        reader.readAsDataURL(file);
    });

    const readFileAsArrayBuffer = (file) => new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => resolve(e.target.result);
        reader.readAsArrayBuffer(file);
    });

    async function processPDF() {
        const pdfInput = document.getElementById('pdfInput');
        if (!pdfInput.files[0]) {
            alert("Please upload a PDF file first.");
            return;
        }

        const processBtn = document.getElementById('processBtn');
        const status = document.getElementById('status');
        const progressBar = document.getElementById('progressBar');
        const progressFill = document.getElementById('progressFill');
        
        processBtn.disabled = true;
        progressBar.style.display = 'block';
        
        try {
            status.innerText = "Reading PDF file...";
            progressFill.style.width = "5%";

            // 1. Read existing PDF
            const pdfBuffer = await readFileAsArrayBuffer(pdfInput.files[0]);
            const pdf = await pdfjsLib.getDocument({ data: pdfBuffer }).promise;
            const totalPages = pdf.numPages;
            
            // 2. Prepare Watermarks
            const wmImgFile = document.getElementById('watermarkImage').files[0];
            const wmOpacity = parseFloat(document.getElementById('watermarkOpacity').value) / 100;
            const wmImgSizePercent = parseFloat(document.getElementById('watermarkImgSize').value) / 100;
            
            let wmImgData = null;
            if (wmImgFile) {
                wmImgData = await readFileAsDataURL(wmImgFile);
            }

            const wmText = document.getElementById('watermarkText').value.trim();
            const wmFontSize = parseInt(document.getElementById('watermarkFontSize').value);
            const wmColor = hexToRgb(document.getElementById('watermarkColor').value);
            const wmAngle = parseInt(document.getElementById('watermarkAngle').value);

            const pageText = document.getElementById('pageText').value.trim();
            const pageNumbersRaw = document.getElementById('pageNumbers').value.trim();
            const targetPages = pageNumbersRaw ? pageNumbersRaw.split(',').map(n => parseInt(n.trim())) : [];
            const quality = parseFloat(document.getElementById('compressionLevel').value);

            let newPdf = null;

            // 3. Process each page
            for (let i = 1; i <= totalPages; i++) {
                status.innerText = `Processing page ${i} of ${totalPages}...`;
                progressFill.style.width = `${((i) / totalPages) * 90}%`;

                const page = await pdf.getPage(i);
                
                // Scale 2.0 keeps text crisp during rendering before JPEG compression kicks in
                const viewport = page.getViewport({ scale: 2.0 }); 
                
                // Render page to canvas
                const canvas = document.createElement('canvas');
                const ctx = canvas.getContext('2d');
                canvas.width = viewport.width;
                canvas.height = viewport.height;

                await page.render({ canvasContext: ctx, viewport: viewport }).promise;

                // Compress canvas to JPEG
                const imgData = canvas.toDataURL('image/jpeg', quality);

                // Initialize new PDF on first page with dimensions matching the original page
                const pdfWidth = viewport.width / 2.0 * 0.264583; // Convert px to mm
                const pdfHeight = viewport.height / 2.0 * 0.264583;

                if (i === 1) {
                    newPdf = new jsPDF({ orientation: pdfWidth > pdfHeight ? 'landscape' : 'portrait', unit: 'mm', format: [pdfWidth, pdfHeight] });
                } else {
                    newPdf.addPage([pdfWidth, pdfHeight], pdfWidth > pdfHeight ? 'landscape' : 'portrait');
                }

                // Add compressed page image
                newPdf.addImage(imgData, 'JPEG', 0, 0, pdfWidth, pdfHeight);

                // Add Image Watermark
                if (wmImgData) {
                    newPdf.setGState(new newPdf.GState({opacity: wmOpacity}));
                    
                    // Estimate size based on user customization
                    const wmSize = Math.min(pdfWidth, pdfHeight) * wmImgSizePercent; 
                    const wmX = (pdfWidth - wmSize) / 2;
                    const wmY = (pdfHeight - wmSize) / 2;
                    
                    newPdf.addImage(wmImgData, 'JPEG', wmX, wmY, wmSize, wmSize);
                    newPdf.setGState(new newPdf.GState({opacity: 1.0}));
                }

                // Add Text Watermark
                if (wmText) {
                    newPdf.setGState(new newPdf.GState({opacity: 0.3})); 
                    newPdf.setTextColor(wmColor.r, wmColor.g, wmColor.b);
                    newPdf.setFontSize(wmFontSize);
                    
                    newPdf.text(wmText, pdfWidth / 2, pdfHeight / 2, { align: 'center', angle: wmAngle, baseline: 'middle' });
                    newPdf.setGState(new newPdf.GState({opacity: 1.0}));
                }

                // Add Page Text (Bottom)
                if (pageText) {
                    if (targetPages.length === 0 || targetPages.includes(i)) {
                        newPdf.setTextColor(0, 0, 0); 
                        newPdf.setFontSize(10);
                        newPdf.text(pageText, pdfWidth / 2, pdfHeight - 10, { align: 'center' });
                    }
                }
            }

            status.innerText = "Generating Final PDF...";
            progressFill.style.width = "100%";
            
            // Download the file
            newPdf.save(`edited_${pdfInput.files[0].name}`);
            status.innerText = "Done! File downloaded.";
            
        } catch (error) {
            console.error(error);
            alert("An error occurred. Check console.");
            status.innerText = "Error processing PDF.";
        } finally {
            processBtn.disabled = false;
            setTimeout(() => { 
                progressBar.style.display = 'none';
                if(status.innerText.includes("Done")) status.innerText = ""; 
            }, 3000);
        }
    }
</script>

</body>
</html>
