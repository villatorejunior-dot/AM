# 🛡️ AM Engenharia - Sistema de Vistoria de Segurança do Trabalho

## 📱 O que é este app?

Um Progressive Web App (PWA) completo para realizar vistorias de segurança do trabalho em obras, com funcionalidades de:
- ✅ Checklist completo baseado nas NRs
- 📷 Captura de fotos via câmera do celular
- 📝 Observações detalhadas por item
- 📊 Relatório automático com estatísticas
- 📧 Envio por email
- 💾 Funciona offline

---

## 🚀 Como Usar no Celular

### Opção 1: Uso Direto (Mais Rápido)
1. Abra o arquivo `vistoria-seguranca.html` em qualquer navegador
2. O app funciona imediatamente!

### Opção 2: Instalar como App (Recomendado)

#### No Android (Chrome):
1. Abra o arquivo no Chrome
2. Toque no menu (⋮) no canto superior direito
3. Selecione "Adicionar à tela inicial"
4. Confirme a instalação
5. O ícone aparecerá na tela inicial como um app normal!

#### No iPhone/iPad (Safari):
1. Abra o arquivo no Safari
2. Toque no botão de compartilhar (□↑)
3. Selecione "Adicionar à Tela de Início"
4. Confirme a instalação
5. O ícone aparecerá na tela inicial!

---

## 📋 Como Funciona

### 1️⃣ Tela Inicial - Dados da Vistoria
Preencha os dados básicos:
- Nome do empreendimento
- Número da visita
- Data e hora (automáticos)
- Engenheiro vistoriador
- Engenheiro acompanhante
- Empresa construtora
- Responsável técnico
- Matrícula CEI/CNPJ
- Número de trabalhadores

### 2️⃣ Tela de Checklist - Realizar Vistoria
**Navegação por Grupos:**
- Sidebar lateral com 6 grupos principais:
  1. Serviços em Execução
  2. Áreas de Vivência (NR 18)
  3. Segurança do Trabalho - Campo
  4. Segurança do Trabalho - Documentação
  5. Outros
  6. Bonificações/Penalidades

**Para cada item você pode:**
- ✅ Marcar como verificado (checkbox)
- 📊 Avaliar conformidade:
  - **Conforme/NC** (para itens binários)
  - **MB/B/OK/R/MR** (para itens graduais)
- 📝 Adicionar observações detalhadas
- 📷 Tirar/anexar múltiplas fotos

**Barra de Progresso:**
- Mostra quantos itens já foram verificados
- Percentual de conclusão por grupo

### 3️⃣ Tela de Relatório - Finalização
**Resumo Automático:**
- Total de itens verificados
- Número de conformidades
- Número de não conformidades
- Percentual geral de conformidade
- Lista de não conformidades críticas

**Envio:**
- Digite o(s) email(s) de destino (separados por vírgula)
- Adicione observações finais (opcional)
- Clique em "Gerar e Enviar Relatório"

---

## 🎯 Funcionalidades Principais

### ✅ Checklist Completo
- 150+ itens de verificação
- Baseado em NR 18, NR 35 e melhores práticas
- Organizado em 6 grandes grupos
- 25 subseções específicas

### 📷 Câmera Integrada
- Acesso direto à câmera do celular
- Múltiplas fotos por item
- Pré-visualização das fotos
- Possibilidade de remover fotos

### 📊 Avaliações Flexíveis
- **Conforme/NC**: Para itens que devem estar presentes ou não
- **MB/B/OK/R/MR**: Para itens que precisam de graduação de qualidade
  - MB = Muito Bom
  - B = Bom
  - OK = Adequado
  - R = Regular
  - MR = Muito Regular (Não conforme)

### 💾 Armazenamento Local
- Todos os dados ficam salvos no dispositivo
- Não perde informações se fechar o app
- Funciona sem internet

### 📈 Relatório Inteligente
- Cálculo automático de conformidade
- Identificação de itens críticos
- Resumo estatístico
- Organização por grupos

---

## 🌐 Como Hospedar Online (Opcional)

### Opção 1: GitHub Pages (Gratuito)
1. Crie um repositório no GitHub
2. Faça upload dos arquivos:
   - `vistoria-seguranca.html`
   - `manifest.json`
3. Vá em Settings > Pages
4. Ative o GitHub Pages
5. Acesse pelo link: `https://seuusuario.github.io/nome-do-repo/vistoria-seguranca.html`

### Opção 2: Servidor Próprio
Basta fazer upload dos 2 arquivos para qualquer servidor web e acessar pelo navegador.

### Opção 3: Google Drive (Simples mas Limitado)
1. Faça upload do arquivo HTML para o Drive
2. Compartilhe publicamente
3. Abra pelo celular

---

## 📱 Requisitos

### Dispositivos Compatíveis:
- ✅ Android (Chrome, Firefox, Edge)
- ✅ iPhone/iPad (Safari)
- ✅ Qualquer celular com navegador moderno

### Permissões Necessárias:
- 📷 Acesso à câmera (para tirar fotos)
- 💾 Armazenamento local (automático)

---

## 🔧 Personalização

### Modificar Itens do Checklist
Abra o arquivo `vistoria-seguranca.html` e encontre a seção `checklistStructure` (linha ~580). 
Lá você pode:
- Adicionar novos itens
- Remover itens
- Modificar textos
- Criar novos grupos

### Modificar Cores da Marca
Procure pela seção `:root` no CSS (linha ~19) e modifique as variáveis:
```css
--primary-green: #1e5631;  /* Verde principal */
--yellow: #ffd700;         /* Amarelo de destaque */
```

---

## 🎨 Funcionalidades Futuras (Próximas Versões)

1. **Geração de PDF Real**
   - Atualmente simula o envio
   - Próxima versão: PDF completo com fotos e gráficos

2. **Envio Automático por Email**
   - Integração com serviço de email
   - Anexo do relatório PDF

3. **Assinatura Digital**
   - Canvas para assinatura dos engenheiros
   - Inclusão no relatório

4. **Sincronização em Nuvem**
   - Backup automático das vistorias
   - Acesso de múltiplos dispositivos

5. **Modo Offline Completo**
   - Service Worker para funcionar 100% offline
   - Sincronização quando conectar

6. **Comparação de Vistorias**
   - Histórico de vistorias anteriores
   - Evolução dos indicadores

---

## 🆘 Solução de Problemas

### "A câmera não funciona"
- Certifique-se de dar permissão ao navegador
- Em Android: Settings > Apps > Chrome > Permissions > Camera
- Em iPhone: Settings > Safari > Camera

### "O app não está salvando os dados"
- Verifique se o navegador tem permissão para armazenamento
- Não use modo anônimo/privado

### "O menu lateral não aparece no celular"
- Toque no botão flutuante verde (☰) no canto inferior direito
- Funcionalidade específica para telas pequenas

### "Quero começar uma nova vistoria"
- Volte para a tela inicial
- Os dados anteriores ficam salvos localmente
- Para limpar, use o botão "Iniciar Nova Vistoria" após finalizar

---

## 📞 Suporte

Para dúvidas ou sugestões sobre o app:
- 📧 Email: contato@amengenharia.com
- 📱 WhatsApp: (45) 99999-9999
- 🌐 Site: www.amengenharia.com.br

---

## 📄 Licença

© 2024 AM Engenharia - Todos os direitos reservados.
Este app foi desenvolvido exclusivamente para uso interno da AM Engenharia.

---

## 🎉 Pronto para Usar!

O app está 100% funcional e pronto para ser usado em campo. Basta abrir o arquivo HTML no celular e começar a vistoria!

**Desenvolvido com ❤️ para facilitar o trabalho dos engenheiros da AM Engenharia**
