# 🔧 Documentação Técnica - Sistema de Vistoria

## 📐 Arquitetura do Sistema

### Tecnologias Utilizadas
- **HTML5**: Estrutura e semântica
- **CSS3**: Estilização responsiva e mobile-first
- **JavaScript (ES6+)**: Lógica de negócio e interatividade
- **PWA**: Progressive Web App para instalação
- **LocalStorage**: Armazenamento persistente local
- **FileReader API**: Processamento de imagens
- **Media Capture API**: Acesso à câmera

### Estrutura de Dados

```javascript
vistoriaData = {
  info: {
    nomeEmpreendimento: string,
    numeroVisita: number,
    dataVisita: string,
    horaInicio: string,
    engenheiroVistoriador: string,
    engenheiroAcompanhante: string,
    empresaConstrutora: string,
    responsavelTecnico: string,
    matriculaCEI: string,
    numeroTrabalhadores: number
  },
  checklist: {
    "1.1.0": {
      text: string,
      type: "conform" | "rating" | "description" | "critical",
      checked: boolean,
      evaluation: "Conforme" | "NC" | "MB" | "B" | "OK" | "R" | "MR",
      observation: string,
      photos: [base64_string, ...]
    }
  },
  startTime: Date,
  endTime: Date,
  summary: {
    total: number,
    checked: number,
    conforme: number,
    naoConforme: number,
    critical: [...],
    byGroup: {...}
  }
}
```

---

## 🚀 Implementações Futuras

### 1. Geração Real de PDF com jsPDF

```javascript
async function gerarPDFReal() {
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    
    // Cabeçalho
    doc.setFontSize(18);
    doc.setTextColor(30, 86, 49);
    doc.text('AM ENGENHARIA', 20, 20);
    doc.setFontSize(14);
    doc.text('Relatório de Vistoria de Segurança do Trabalho', 20, 30);
    
    // Dados do empreendimento
    doc.setFontSize(10);
    doc.text(`Empreendimento: ${vistoriaData.info.nomeEmpreendimento}`, 20, 45);
    doc.text(`Visita Nº: ${vistoriaData.info.numeroVisita}`, 20, 52);
    doc.text(`Data: ${vistoriaData.info.dataVisita}`, 20, 59);
    
    // Adicionar gráficos com Chart.js + html2canvas
    const canvas = await html2canvas(document.getElementById('chartContainer'));
    const imgData = canvas.toDataURL('image/png');
    doc.addImage(imgData, 'PNG', 20, 70, 170, 80);
    
    // Adicionar fotos dos itens
    let yPosition = 160;
    for (let itemId in vistoriaData.checklist) {
        const item = vistoriaData.checklist[itemId];
        if (item.photos.length > 0) {
            if (yPosition > 250) {
                doc.addPage();
                yPosition = 20;
            }
            doc.text(item.text, 20, yPosition);
            yPosition += 7;
            
            item.photos.forEach(photo => {
                if (yPosition > 240) {
                    doc.addPage();
                    yPosition = 20;
                }
                doc.addImage(photo, 'JPEG', 20, yPosition, 60, 60);
                yPosition += 65;
            });
        }
    }
    
    // Salvar
    doc.save(`vistoria_${vistoriaData.info.numeroVisita}_${Date.now()}.pdf`);
}
```

### 2. Envio por Email via API

```javascript
async function enviarPorEmail(pdfBase64, emails) {
    // Opção 1: EmailJS (gratuito até 200 emails/mês)
    emailjs.send("service_id", "template_id", {
        to_emails: emails,
        pdf_attachment: pdfBase64,
        subject: `Relatório de Vistoria - ${vistoriaData.info.nomeEmpreendimento}`,
        message: "Segue anexo o relatório de vistoria de segurança do trabalho."
    });
    
    // Opção 2: Backend próprio
    await fetch('https://api.amengenharia.com/send-report', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            emails: emails.split(','),
            pdf: pdfBase64,
            vistoriaData: vistoriaData
        })
    });
}
```

### 3. Service Worker para Offline Completo

```javascript
// sw.js
const CACHE_NAME = 'am-vistoria-v1';
const urlsToCache = [
  '/vistoria-seguranca.html',
  '/manifest.json',
  'https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js',
  'https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js'
];

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(urlsToCache))
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
});

// Registrar no HTML:
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
}
```

### 4. Sincronização com Backend

```javascript
// Salvar no IndexedDB
async function salvarVistoria() {
    const db = await openDB('am-vistorias', 1, {
        upgrade(db) {
            db.createObjectStore('vistorias', { keyPath: 'id', autoIncrement: true });
        }
    });
    
    await db.add('vistorias', {
        ...vistoriaData,
        timestamp: Date.now(),
        synced: false
    });
}

// Sincronizar quando online
async function sincronizarVistorias() {
    if (!navigator.onLine) return;
    
    const db = await openDB('am-vistorias', 1);
    const vistorias = await db.getAllFromIndex('vistorias', 'synced', false);
    
    for (let vistoria of vistorias) {
        try {
            await fetch('https://api.amengenharia.com/vistorias', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(vistoria)
            });
            
            vistoria.synced = true;
            await db.put('vistorias', vistoria);
        } catch (err) {
            console.error('Erro ao sincronizar:', err);
        }
    }
}
```

### 5. Assinatura Digital

```html
<!-- Adicionar no HTML -->
<canvas id="signatureCanvas" width="400" height="200"></canvas>
<button onclick="clearSignature()">Limpar</button>
<button onclick="saveSignature()">Salvar</button>

<script>
const canvas = document.getElementById('signatureCanvas');
const ctx = canvas.getContext('2d');
let isDrawing = false;

canvas.addEventListener('mousedown', startDrawing);
canvas.addEventListener('mousemove', draw);
canvas.addEventListener('mouseup', stopDrawing);

// Touch events para mobile
canvas.addEventListener('touchstart', (e) => {
    e.preventDefault();
    const touch = e.touches[0];
    const mouseEvent = new MouseEvent('mousedown', {
        clientX: touch.clientX,
        clientY: touch.clientY
    });
    canvas.dispatchEvent(mouseEvent);
});

function startDrawing(e) {
    isDrawing = true;
    ctx.beginPath();
    ctx.moveTo(e.offsetX, e.offsetY);
}

function draw(e) {
    if (!isDrawing) return;
    ctx.lineTo(e.offsetX, e.offsetY);
    ctx.stroke();
}

function stopDrawing() {
    isDrawing = false;
}

function clearSignature() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
}

function saveSignature() {
    const signatureData = canvas.toDataURL('image/png');
    vistoriaData.signature = signatureData;
}
</script>
```

### 6. Gráficos de Conformidade com Chart.js

```javascript
function criarGraficoConformidade() {
    const ctx = document.getElementById('chartCanvas').getContext('2d');
    
    const data = {
        labels: Object.keys(vistoriaData.summary.byGroup).map(key => 
            checklistStructure[key].title
        ),
        datasets: [{
            label: 'Conformidade (%)',
            data: Object.values(vistoriaData.summary.byGroup).map(group => 
                group.checked > 0 ? (group.conforme / group.checked * 100) : 0
            ),
            backgroundColor: [
                'rgba(30, 86, 49, 0.8)',
                'rgba(45, 122, 74, 0.8)',
                'rgba(40, 167, 69, 0.8)',
                'rgba(255, 193, 7, 0.8)',
                'rgba(255, 140, 0, 0.8)',
                'rgba(220, 53, 69, 0.8)'
            ]
        }]
    };
    
    new Chart(ctx, {
        type: 'bar',
        data: data,
        options: {
            responsive: true,
            scales: {
                y: {
                    beginAtZero: true,
                    max: 100
                }
            }
        }
    });
}
```

### 7. Exportar para Excel

```javascript
// Usando SheetJS (xlsx)
function exportarParaExcel() {
    const wb = XLSX.utils.book_new();
    
    // Sheet 1: Resumo
    const resumo = [
        ['AM ENGENHARIA - RELATÓRIO DE VISTORIA'],
        [],
        ['Empreendimento:', vistoriaData.info.nomeEmpreendimento],
        ['Visita Nº:', vistoriaData.info.numeroVisita],
        ['Data:', vistoriaData.info.dataVisita],
        [],
        ['RESUMO'],
        ['Total de Itens:', vistoriaData.summary.total],
        ['Itens Verificados:', vistoriaData.summary.checked],
        ['Conformes:', vistoriaData.summary.conforme],
        ['Não Conformes:', vistoriaData.summary.naoConforme]
    ];
    const ws1 = XLSX.utils.aoa_to_sheet(resumo);
    XLSX.utils.book_append_sheet(wb, ws1, 'Resumo');
    
    // Sheet 2: Checklist Detalhado
    const checklist = [['Item', 'Avaliação', 'Observação']];
    for (let itemId in vistoriaData.checklist) {
        const item = vistoriaData.checklist[itemId];
        if (item.checked) {
            checklist.push([
                item.text,
                item.evaluation || 'N/A',
                item.observation || ''
            ]);
        }
    }
    const ws2 = XLSX.utils.aoa_to_sheet(checklist);
    XLSX.utils.book_append_sheet(wb, ws2, 'Checklist');
    
    // Baixar
    XLSX.writeFile(wb, `vistoria_${vistoriaData.info.numeroVisita}.xlsx`);
}
```

### 8. Compressão de Imagens

```javascript
function compressImage(base64, maxWidth = 1024) {
    return new Promise((resolve) => {
        const img = new Image();
        img.onload = () => {
            const canvas = document.createElement('canvas');
            let width = img.width;
            let height = img.height;
            
            if (width > maxWidth) {
                height = (maxWidth / width) * height;
                width = maxWidth;
            }
            
            canvas.width = width;
            canvas.height = height;
            
            const ctx = canvas.getContext('2d');
            ctx.drawImage(img, 0, 0, width, height);
            
            resolve(canvas.toDataURL('image/jpeg', 0.8));
        };
        img.src = base64;
    });
}

// Usar ao adicionar foto
async function addPhoto(itemId, input) {
    const file = input.files[0];
    if (!file) return;
    
    const reader = new FileReader();
    reader.onload = async (e) => {
        const compressed = await compressImage(e.target.result);
        vistoriaData.checklist[itemId].photos.push(compressed);
        // ... resto do código
    };
    reader.readAsDataURL(file);
}
```

---

## 🔐 Segurança e Boas Práticas

### Validação de Dados
- Sempre validar inputs do usuário
- Sanitizar campos de texto antes de salvar
- Limitar tamanho de uploads de fotos

### Performance
- Comprimir imagens antes de armazenar
- Usar lazy loading para fotos
- Otimizar renderização de listas longas

### Backup
- Implementar export/import de dados
- Sincronização periódica com servidor
- Manter histórico de versões

---

## 📊 Métricas e Analytics

```javascript
// Implementar tracking de uso
function trackEvent(category, action, label) {
    // Google Analytics
    gtag('event', action, {
        event_category: category,
        event_label: label
    });
    
    // Ou enviar para backend próprio
    fetch('/api/analytics', {
        method: 'POST',
        body: JSON.stringify({ category, action, label, timestamp: Date.now() })
    });
}

// Exemplos de tracking
trackEvent('Vistoria', 'Iniciada', vistoriaData.info.nomeEmpreendimento);
trackEvent('Checklist', 'Item Marcado', itemId);
trackEvent('Relatorio', 'Gerado', vistoriaData.info.numeroVisita);
```

---

## 🧪 Testes

### Testes Manuais Recomendados
1. Preencher formulário inicial e verificar salvamento
2. Marcar diversos itens do checklist
3. Tirar fotos e verificar preview
4. Adicionar observações longas
5. Navegar entre grupos
6. Finalizar vistoria e verificar resumo
7. Testar em modo offline
8. Testar em diferentes tamanhos de tela

### Checklist de Compatibilidade
- [ ] Chrome Android
- [ ] Safari iOS
- [ ] Firefox Android
- [ ] Samsung Internet
- [ ] Chrome Desktop (para desenvolvimento)

---

## 📞 Contato do Desenvolvedor

Para suporte técnico ou customizações:
- Este app foi desenvolvido com Claude AI
- Documentação completa disponível no código-fonte
- Comentários inline para facilitar manutenção

---

**Última atualização: Dezembro 2024**
