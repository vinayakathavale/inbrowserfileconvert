<script>
    import { onMount } from 'svelte';
  
    let fileInput;
    let pdfjs;
  
    onMount(async () => {
      const script = document.createElement('script');
      script.src = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.10.377/pdf.min.js';
      document.head.appendChild(script);
      await new Promise(resolve => {
        script.onload = () => {
          pdfjs = window.pdfjsLib;
          resolve();
        };
      });
  
      fileInput.addEventListener('change', handleFileUpload);
    });
  
    // This function will extract all the text from the first page of the PDF
    async function extractTextFromPdf(pdfArrayBuffer) {
      const pdf = await pdfjs.getDocument({data: pdfArrayBuffer}).promise;
      const page = await pdf.getPage(1);
      const content = await page.getTextContent();
      const text = content.items.map(item => item.str).join(' ') + '\n\n';
      return text;
    }
  
    // This function will handle the file upload and display the extracted text
    async function handleFileUpload(event) {
      const file = event.target.files[0];
      if (file.type !== 'application/pdf') {
        alert('Please upload a PDF file');
        return;
      }
  
      const reader = new FileReader();
      reader.onload = async function() {
        const text = await extractTextFromPdf(reader.result);
        document.getElementById('output').textContent = text;
      };
      reader.readAsArrayBuffer(file);
    }
  </script>
  
  <input type="file" bind:this={fileInput} />
  <div id="output"></div>