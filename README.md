# 🏥 Fila Monitor — estudo de caso

> Aplicativo desktop que monitora a fila de pacientes de uma plataforma de telemedicina e avisa o médico quando há alguém aguardando atendimento.
> **Em uso diário por 4 médicos.**

*Este repositório contém apenas a descrição do projeto. O código-fonte é privado e não é publicado aqui, e nenhum dado de paciente, médico ou da operadora de saúde é exibido.*

---

## 🧩 O problema

Médicos que atendem por telemedicina sob demanda recebem pacientes de uma **fila compartilhada** num portal web. Para pegar um atendimento, o médico precisa ficar com o portal aberto, atualizando a página manualmente e reagindo rápido quando surge alguém aguardando.

Na prática isso significa:

- tempo ocioso olhando para a tela em vez de descansar ou preparar o próximo atendimento;
- pacientes esperando mais do que o necessário quando o médico não percebe a entrada na fila;
- nenhum aviso sonoro: se o médico sai da frente do computador, perde o paciente.

## 💡 A solução

Um aplicativo desktop leve, que roda no Windows do médico e:

1. **Monitora a fila** continuamente em segundo plano;
2. Ao encontrar um paciente aguardando, **atribui o atendimento ao médico** e **pausa a busca**;
3. **Avisa com voz** ("Novo paciente atribuído"), com beep como reserva, para que o médico possa estar longe da tela;
4. Mostra um **log de eventos** na janela (busca, atribuição, erros, pausas).

O médico só abre o app, informa o acesso da sessão e clica em **Iniciar**.

## 🛠️ Stack

**Python** · **CustomTkinter** (interface) · requests (HTTP) · PyInstaller (empacotamento em `.exe`)

## ⚙️ Decisões de engenharia

| Desafio | Como resolvi |
|---|---|
| A janela não pode travar enquanto a busca roda | A busca roda numa **thread separada**. Ela não mexe na interface diretamente: coloca eventos numa fila que a thread principal lê a cada 100 ms (comunicação *thread-safe*). |
| A plataforma limita o número de requisições | **Backoff exponencial** ao receber HTTP 429 e **pausa longa** após vários erros seguidos, para não sobrecarregar o servidor. |
| Falhas de rede no meio de uma atribuição | Erros tratados por tipo (acesso expirado, rede, paciente já atendido por outro médico, atribuição pendente). Em caso de dúvida, o app relista a fila, que é a fonte da verdade, em vez de assumir o resultado. |
| O acesso da sessão expira durante o expediente | O médico troca o acesso **sem reiniciar** o app; a busca continua de onde parou. |
| Testar sem depender do sistema real | **Modo de simulação** que gera uma fila falsa para testar interface e alertas. |
| Privacidade de pacientes (LGPD) | Por padrão, **nenhum dado de paciente vai para logs** ou para o terminal. Credenciais ficam num arquivo local fora do controle de versão. |
| Um executável por médico | Todo o código é igual; só um arquivo de perfil muda. Gerar a versão de um novo médico é trocar esse arquivo e reempacotar. |

## 📈 Resultado

- **Em uso por 4 médicos** no dia a dia de atendimento por telemedicina.
- Os médicos deixaram de acompanhar a fila manualmente: o app avisa quando há paciente.
- Distribuição simples: cada médico recebe um `.exe` próprio, sem precisar instalar Python.

## 🧠 O que aprendi

- Programação concorrente com threads e comunicação segura com a interface gráfica;
- Tratamento robusto de erros de rede e limites de requisição;
- Empacotamento e distribuição de apps Python para usuários não técnicos;
- Cuidados com dados sensíveis de saúde desde o design.

---

👤 **Dimitri Gomes de Albuquerque** · [GitHub](https://github.com/Dimitri7) · [LinkedIn](https://www.linkedin.com/in/dimitri-albuquerque-66b32b278/)
