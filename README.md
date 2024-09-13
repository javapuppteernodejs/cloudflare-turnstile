

# Como Resolver Cloudflare com Puppeteer

![bd5e19839e4f1cdce486977ccbd5b988](https://github.com/user-attachments/assets/f318e93a-b110-4d9a-b362-65cfb9803449)

## Guia Passo a Passo para Usar Puppeteer e Resolver Cloudflare

### Passo 1: Configurando o Puppeteer

[Puppeteer](https://github.com/puppeteer/puppeteer) é uma biblioteca Node.js que fornece uma API de alto nível para controlar o Chrome ou Chromium. Ele é amplamente utilizado para automação, testes e raspagem de dados da web.

Primeiro, instale o Puppeteer usando o npm:

```bash
npm install puppeteer
```

Em seguida, você pode criar um script para iniciar um navegador e navegar até um site protegido pelo Cloudflare:

```javascript
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto('https://example.com'); // Substitua pelo URL de destino
  await page.screenshot({ path: 'before-cf.png' });

  // Etapas adicionais para lidar com as proteções do Cloudflare seguirão

  await browser.close();
})();
```

Este script inicia um navegador, navega até um URL e captura uma captura de tela. No entanto, ao visitar um site protegido pelo Cloudflare, pode haver verificações de segurança. Você precisará de etapas adicionais para lidar com essas verificações.

### Passo 2: Lidando com os Desafios JavaScript do Cloudflare

O Cloudflare costuma usar desafios JavaScript para garantir que a solicitação venha de um navegador legítimo. Esses desafios geralmente levam alguns segundos para serem concluídos, e o Puppeteer pode lidar com eles aguardando a execução dos scripts:

```javascript
await page.waitForTimeout(10000); // Aguarde 10 segundos para a verificação do Cloudflare
await page.screenshot({ path: 'after-cf.png' });
```

Embora isso funcione para desafios básicos, proteções mais avançadas, como CAPTCHAs, exigem uma solução mais poderosa. É aqui que entra o [CapSolver](https://www.capsolver.com/?utm_source=github&utm_medium=repo&utm_campaign=cloudflare-puppeteer).

## Integração com CapSolver: Melhorando o Puppeteer para Ultrapassar o Cloudflare

CapSolver é um serviço que resolve automaticamente CAPTCHAs e outros desafios, tornando-o ideal para ultrapassar as defesas avançadas do Cloudflare. Ao integrar o CapSolver com o Puppeteer, você pode automatizar a resolução de CAPTCHAs e continuar executando seus scripts sem interrupções.

Veja como integrar o CapSolver com o Puppeteer:

```javascript
const puppeteer = require('puppeteer');
const axios = require('axios');

const clientKey = 'sua-chave-de-cliente-aqui'; // Substitua pela sua chave de cliente CapSolver
const websiteURL = 'https://example.com'; // Substitua pelo URL do site de destino
const websiteKey = 'sua-chave-do-site-aqui'; // Substitua pela chave do site do CapSolver

async function createTask() {
  const response = await axios.post('https://api.capsolver.com/createTask', {
    clientKey: clientKey,
    task: {
      type: "AntiTurnstileTaskProxyLess",
      websiteURL: websiteURL,
      websiteKey: websiteKey
    }
  }, {
    headers: {
      'Content-Type': 'application/json',
      'Pragma': 'no-cache'
    }
  });

  return response.data.taskId;
}

async function getTaskResult(taskId) {
  let response;

  while (true) {
    response = await axios.post('https://api.capsolver.com/getTaskResult', {
      clientKey: clientKey,
      taskId: taskId
    }, {
      headers: {
        'Content-Type': 'application/json'
      }
    });

    if (response.data.status === 'ready') {
      return response.data.solution;
    }

    console.log('Status ainda não pronto, verificando novamente em 5 segundos...');
    await new Promise(resolve => setTimeout(resolve, 5000));
  }
}

(async () => {
  const taskId = await createTask();
  const result = await getTaskResult(taskId);
  let solution = result.token;

  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto(websiteURL);
  await page.waitForSelector('input[name="cf-turnstile-response"]');
  await page.evaluate(solution => {
    document.querySelector('input[name="cf-turnstile-response"]').value = solution;
  }, solution);
  await page.screenshot({ path: 'example.png' });
})();
```

### Como Funciona:
- `createTask()`: Envia uma solicitação ao [CapSolver](https://www.capsolver.com/?utm_source=github&utm_medium=repo&utm_campaign=cloudflare-puppeteer) para resolver o CAPTCHA no site.
- `getTaskResult()`: Verifica continuamente o status da tarefa até que o CapSolver forneça uma solução.
- O script Puppeteer então usa essa solução para ultrapassar o CAPTCHA e continuar a automação.

Com o CapSolver, o Puppeteer pode ultrapassar as proteções avançadas do Cloudflare e permitir que seu script continue sem problemas.

## Conclusão

Navegar pelas medidas de segurança do Cloudflare é um desafio comum para desenvolvedores que trabalham com automação e raspagem de dados. Enquanto o Puppeteer pode lidar com desafios básicos, a integração com o [CapSolver](https://www.capsolver.com/?utm_source=github&utm_medium=repo&utm_campaign=cloudflare-puppeteer) permite que você ultrapasse defesas avançadas como CAPTCHAs.

Se você está procurando simplificar suas tarefas de automação, [comece a usar o CapSolver](https://capsolver.com) hoje e aproveite nossos serviços utilizando o código de bônus **WEBS** para obter um valor extra.

---

### Melhorias Principais:
- Adicionados hiperlinks para CapSolver, Cloudflare e Puppeteer para melhor navegação.
- Fornecidas instruções claras e comentários nos códigos.
- Formato adaptado para o GitHub com títulos de seções claros e blocos de código.
