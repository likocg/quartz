---
{"publish":true,"created":"2025-07-13T16:15","modified":"2025-07-13T16:22","published":"2025-07-15T18:35:43.049-03:00","tags":["notas","n8n"],"cssclasses":""}
---

# Como manter o n8n sempre ativo

Quando você inicia o n8n com um comando como

```bash
n8n
# ou
n8n start
```

ele roda como um processo em primeiro-plano ligado àquela sessão do
Terminal. Se fechar o terminal (ou der Ctrl-C), o processo termina e:

• a UI deixa de responder;  
• todos os triggers (Schedule, Webhook, etc.) param;  
• workflows marcam “Inactive (Instance offline)” até o serviço voltar.

Como mantê-lo sempre ativo
--------------------------

1. **PM2** (recomendado para Mac / Linux)
   ```bash
   npm install -g pm2
   pm2 start n8n --name n8n
   pm2 save              # grava para reiniciar no boot
   pm2 startup           # gera o comando de autostart e siga a instrução
   ```
   – O processo vira serviço e continua mesmo depois de logout ou reboot.

2. **systemd** (Linux)
   Crie `/etc/systemd/system/n8n.service`:
   ```
   [Unit]
   Description=n8n
   After=network.target

   [Service]
   ExecStart=/usr/bin/n8n
   Restart=always
   User=<seu-usuario>
   Environment=NODE_ENV=production

   [Install]
   WantedBy=multi-user.target
   ```
   ```bash
   sudo systemctl enable n8n
   sudo systemctl start  n8n
   ```

3. **Docker**  
   Rodar com `--restart unless-stopped`:
   ```bash
   docker run -d --name n8n \
     -p 5678:5678 \
     --restart unless-stopped \
     -v ~/.n8n:/home/node/.n8n \
     n8nio/n8n
   ```

4. **nohup / &** (solução rápida)
   ```bash
   nohup n8n > n8n.log 2>&1 &
   ```
   – Continua mesmo depois de fechar o terminal, mas não reinicia no boot.

Se só está testando localmente e fechar o terminal não é problema,
basta executar de novo quando precisar.  
Para produção ou execução 24×7 use uma das opções acima para que seus
workflows permaneçam ativos.


---


Manter o n8n rodando indefinidamente em um terminal “solto” não causa danos ao computador, mas há alguns efeitos colaterais/limitações a considerar:

1. Queda acidental encerra tudo  
   • Fechar o terminal, reiniciar a máquina ou perder energia mata o processo → todos os gatilhos param e execuções agendadas são perdidas.

2. Sem inicialização automática  
   • Depois de um reboot você precisa lembrar de abrir o terminal e rodar `n8n` de novo; caso contrário, nada volta a funcionar.

3. Consumo de recursos permanente  
   • n8n fica escutando na porta 5678, executando cron‐jobs, mantendo conexões…  
   • Em desktops isso não é problema; em notebooks pode reduzir autonomia da bateria se você o mantém 24 × 7 ligado.

4. Logs e arquivos crescem sem rotação  
   • Toda execução é escrita em SQLite ou no banco que você configurou (padrão: `~/.n8n/database.sqlite`).  
   • Se você roda muitos workflows ou faz polling de APIs a cada minuto, o arquivo pode crescer vários GB ao longo dos meses — ocupar SSD/HDD.

5. Segurança básica  
   • A UI fica aberta em `http://localhost:5678`. Se você mudar o bind para `0.0.0.0` ou usar proxy sem autenticação, qualquer um na rede pode acessar.  
   • O processo armazena credenciais descriptografadas em memória; nada crítico, mas quanto mais tempo ativo, maior a janela de exposição.

6. Ausência de monitoramento / restart  
   • Se o processo travar (ex.: erro JS não tratado ou falta de memória) ele simplesmente para e não reinicia, a menos que você veja o terminal.

7. Atualizações manuais  
   • Para atualizar você precisa: `Ctrl-C`, `npm i -g n8n@latest` (ou `docker pull`), depois rodar de novo.

Efeitos que **não** acontecem  
• Não causa desgaste extra de disco além dos logs normais.  
• Não “superaquece” CPU, pois a maior parte do tempo o n8n fica ocioso (só cron checks).  
• Não interfere em outro software, contanto que a porta 5678 não conflite.

Boas práticas se pretende manter sempre ligado
----------------------------------------------
• Rode via serviço (PM2, systemd ou Docker com `--restart`) — garante restart automático.  
• Use `N8N_HOST=127.0.0.1` ou autenticação básica (`N8N_BASIC_AUTH_*`) se precisar abrir a porta.  
• Configure rotação de execuções (`EXECUTIONS_DATA_PRUNE=true` + intervalos) para que o banco não cresça sem limite.  
• Faça backup do diretório `~/.n8n` (ou volume Docker) periodicamente.

Resumo
---
Rodar no terminal “do jeito que está” funciona para testes ou uso casual, mas:

• Você perde automações ao fechar o shell ou reiniciar.  
• Logs/banco crescem sem controle.  
• Não há restart automático nem proteção de acesso.

Se virar produção (ou algo importante), mova para um gerenciador de serviço e configure rotação de logs/execuções.

---

### Visão geral rápida

| Ferramenta | O que é? | Como mantém o processo vivo? |
|------------|----------|------------------------------|
| **nohup**  | Comando POSIX (`nohup <cmd> &`) que desconecta o programa do terminal e ignora o sinal *SIGHUP* (hang-up). A saída vai, por padrão, para `nohup.out`. | Apenas evita que o processo morra quando você fecha o shell. Não reinicia se cair ou no boot. |
| **PM2**    | Process Manager escrito em Node.js. Roda como “mestre” e gerencia qualquer aplicação (Node, Python, binário…). | Monitora, gera log, faz *restart* automático, e pode gravar a configuração para auto-start no boot (`pm2 save && pm2 startup`). |

---

### Vantagens / Desvantagens

| Critério | **nohup** | **PM2** |
|----------|-----------|---------|
| Instalação | Nenhuma – vem em qualquer Linux/macOS | `npm i -g pm2` |
| Lançar processo | `nohup cmd &` (simples) | `pm2 start cmd --name app` |
| Sobrevivência ao logout | ✅ mantém | ✅ mantém |
| Reinício automático se o app crashar | ❌ não | ✅ padrão |
| Auto-start no boot | ❌ (precisa crontab ou systemd manual) | `pm2 startup` gera script |
| Log separado stdout/stderr | 🚫 (tudo em `nohup.out`, cresce indefinidamente) | ✅ rotação (`pm2 logs`, `pm2 reloadLogs`) |
| Monitoramento / dashboard | ❌ | ✅ `pm2 monit`, API REST, Keymetrics opcional |
| Reload zero-downtime | ❌ | ✅ (`pm2 reload`) |
| Cluster (multi-CPU) | ❌ | ✅ (`pm2 start app.js -i max`) |
| Consumo extra de RAM/CPU | mínimo (só o seu app) | + processo master (~20-30 MB) |
| Portabilidade (Windows) | Limitado (via Git Bash) | Funciona, mas usa serviço do Windows |
| Curva de aprendizado | Quase zero | Um pouco maior |

---

### Quando usar cada um?

* **nohup**  
  • Scripts rápidos, tarefas únicas, servidores pessoais onde um crash não é crítico.  
  • Você mesmo cuida de logs, restart, boot.

* **PM2**  
  • Aplicações que precisam ficar 24 × 7, com reinício automático, logs rotacionados, stat/reload fáceis (ex.: n8n, APIs, bots).  
  • Ambientes de produção ou desenvolvimento contínuo.

Se começar com `nohup` e precisar de algo mais robusto depois, migrar para **PM2** é só instalar o pacote e executar `pm2 start`.




---



Para deixar o n8n rodando com `nohup` você precisa encerrar a instância que está presa ao terminal e relançá-la em segundo-plano. Passo-a-passo:

1. Pare o n8n que está em 1º-plano  
   • No terminal atual pressione **Ctrl + C**.  
   • A linha de comando volta a aparecer.

2. (Opcional) Limpe o arquivo de log anterior  
   ```bash
   rm -f n8n.log
   ```

3. Relance o n8n desacoplado do shell  
   ```bash
   nohup n8n > n8n.log 2>&1 &
   ```
   Explicação rápida:  
   • `nohup` → ignora sinal de logout.  
   • `>` redireciona **stdout** para `n8n.log`.  
   • `2>&1` manda **stderr** para o mesmo arquivo.  
   • `&` coloca o processo em background e devolve o prompt imediatamente.  
   • Aparece algo como `[1] 12345` (PID).

4. Verifique se está rodando  
   ```bash
   ps -p $(pgrep -f "bin/n8n")
   # ou
   tail -f n8n.log
   ```
   Você verá linhas de log aparecendo enquanto o workflow executa.

5. Feche o terminal à vontade  
   ‑ o processo continuará ativo; `n8n.log` vai receber a saída.

6. Parar futuramente  
   ```bash
   pkill -f "bin/n8n"
   ```
   ou use o PID mostrado pelo `ps`.

Dicas rápidas
• Rode o comando a partir da pasta onde queira armazenar `n8n.log`, ou forneça caminho absoluto, ex.: `> /var/log/n8n.log`.  
• Se precisar reiniciar o servidor, você terá de executar o mesmo comando novamente (nohup **não** configura auto-start — para isso use PM2 ou systemd).


---


Para encerrar o n8n que você lançou com  

```bash
nohup n8n > n8n.log 2>&1 &
```

você precisa matar o processo que continua em segundo-plano.  
Existem três maneiras simples:

------------------------------------------------------------
1. Usando o PID exibido pelo `ps`
------------------------------------------------------------
1. Descubra o PID (process ID):

   ```bash
   ps -ef | grep "[b]in/n8n"
   ```

   Saída típica:

   ```
   user   12345  …  /usr/bin/node /usr/local/bin/n8n
   ```

   O número `12345` é o PID.

2. Mate o processo:

   ```bash
   kill 12345
   ```

3. Confirme que parou:

   ```bash
   ps -p 12345
   # não deve retornar nada
   ```

------------------------------------------------------------
2. Um comando só (pkill)
------------------------------------------------------------
Se houver apenas uma instância do n8n rodando:

```bash
pkill -f "bin/n8n"
```

`-f` faz o pkill procurar a string “bin/n8n” em toda a linha de comando e encerra o(s) processo(s) correspondente(s).

------------------------------------------------------------
3. Via nohup.out / n8n.log (Ctrl-C simulado)
------------------------------------------------------------
1. Abra o arquivo de log em modo “follow”:

   ```bash
   tail -f n8n.log
   ```

2. Em outro terminal obtenha o PID:

   ```bash
   echo $(pgrep -f "bin/n8n")
   ```

3. Pressione Ctrl-C nessa linha: não funciona porque está em BG, então use `kill`.

------------------------------------------------------------
Observações
------------------------------------------------------------
• `kill` envia o sinal **SIGTERM** (15) — o n8n fecha limpo.  
• Se por algum motivo não encerrar, use `kill -9 PID` (SIGKILL), mas isso não grava o estado em disco.  
• Depois de matar o processo, você pode relançar com o mesmo comando `nohup … &` quando precisar.

Assim você controla completamente iniciar e parar o n8n rodando com nohup.