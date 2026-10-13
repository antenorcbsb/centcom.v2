# centcom.v2
Dashboard CENTCOM Braga
Painel de informação operacional para o videowall da Central de Comunicações (CENTCOM). Mostra num só ecrã os agendamentos e informações do dia, o plano de comunicações rádio, os veículos inoperacionais e a meteorologia. Os dados vêm de folhas de cálculo preenchidas pela administração e pelos operadores.
	
	
Última atualização	12/10/2026
Responsável técnico	Daniel Correia — [daniel.correia@cm-braga.pt]
Ficheiro principal	`index.html` (também conhecido como `centcom.v2.html`)
> **Nota:** enquanto a dashboard aguarda aprovação do Comando, a ficha técnica não inclui referências institucionais. Essas referências são acrescentadas depois da aprovação.
---
1. Quadros
Quadro	O que mostra
Título	Nome, emblema, data e relógio.
Agendamentos & Informações	Agendamentos e informações em curso ou futuros, com a imagem do veículo por tipologia e o indicador "a decorrer" quando se está dentro do horário. As caixas de agendamento e as caixas de informação têm cores diferentes.
Plano de Comunicações	Teatros de operações e os respetivos canais rádio (BSB, ROB, REPC, SIRESP…), em formato de ecrã de rádio, com 3 teatros por página em duas colunas.
Veículos Inoperacionais	Veículos INOP, com a imagem por tipologia, a data e hora de inoperacionalidade e as observações.
Meteorologia	Temperatura atual, máx/mín, sensação térmica, risco de incêndio (a cor da caixa muda com o perigo), índice FWI e EPE (Estado de Prontidão Especial).
Rodapé	Avisos em movimento com os procedimentos disponíveis para consulta (ex.: Instrução de Trabalho MOD-1.1-00-04_05, na pasta Share › Centro Comunicações).
Modos de visualização
`index.html` mostra todos os quadros.
`index.html?quadro=<nome>` mostra um só quadro em ecrã inteiro.
Valor de `quadro`	Quadro
`Agendamentos`	Agendamentos & Informações
`Comunicações`	Plano de Comunicações
`Veiculos`	Veículos Inoperacionais
`meteo`	Meteorologia
---
2. Fontes de dados
A dashboard lê todas as linhas a partir da linha 2 de cada folha (a linha 1 é a dos cabeçalhos). Os nomes dos cabeçalhos têm de se manter exatamente como estão.


2.1 Google Sheets Comando_dashboard — preenchido pela administração
ID do ficheiro: `https://docs.google.com/spreadsheets/d/1XPVKo_d35yV3k4C1CF82wL43m0ku-wvkcksmcG5CfnQ/edit`

Folha de agendamentos/informações (gid=0)
Coluna	Conteúdo
Tipo	Agendamento ou Informação
Data Inicio	Primeiro dia
Data Fim	Último dia (opcional)
Hora	Horário (ver formatos abaixo)
Descrição	Texto
Meios alocados	Siglas dos veículos. A sigla `EAPH` usa o ícone de capacete.
Equipas	Equipas envolvidas

Regras:
O registo aparece a partir da Data Inicio e desaparece na hora de fim do último dia. Nos dias intermédios fica visível o dia todo.
Em vez de usar a Data Fim, também se podem listar várias datas na coluna de data.
Formatos de Hora aceites: `08:00-17:00`, `08.00-17.00`, `08h00-17h00`, `08H00-17H00`, `08,00-17,00`, `17:00`, `17.00`, `17h`. Um valor único conta como hora de fim.
Quando a hora atual está dentro do horário do dia, o registo mostra a indicação "a decorrer".
Folha EPE
Nível atual do Estado de Prontidão Especial:

Nível	Prontidão / tempo de resposta
1	até 12 h
2	até 6 h
3	até 2 h
4	imediato
Folha de entrada única
Os registos são lançados aqui e encaminhados por Apps Script para a folha certa, ordenados por data (ver secção 3).


2.2 Google Sheets OPTEL_Veiculos — preenchido pelos operadores

ID do ficheiro: `https://docs.google.com/spreadsheets/d/13xtfCezanpDdvjd2cACrK9gIj3Dt12m-YHTnRzVlYOA/edit?gid=1953924764#gid=1953924764`

Folha	gid	Uso
Dados	[a preencher]	Folha de entrada dos operadores (Canais Rádio, Operacionalidade de Veículos, Chamada Geral Rede), com uma caixa "Registar" junto a cada quadro.
Veículos	171672820	Estado atual dos veículos (lido pela dashboard).
Canais Radio	752912776	Plano de comunicações (lido pela dashboard).
Histórico Veículos	[a preencher]	Histórico de inoperacionalidades.
Chamada Geral Rede / Historico CGR	[a preencher]	Registo das chamadas gerais.
Colunas da folha Veículos: Tipologia Veículo, Estado Operacional, Data Inop., Hora, Observações, Data Operacionalidade, Hora, OP. BSB, OP. CSR.
OP. BSB e OP. CSR são obrigatórios tanto na inoperacionalidade como na reposição.

2.3 Meteorologia

distrits-islands.json - Identifica o código de Braga (local, área de aviso e concelho)

forecast/meteorology/cities/daily/{id}.json - Previsão diária, incluindo máximas e mínimas

forecast/warnings/warnings_www.json - Avisos meteorológicos amarelo, laranja e vermelho da área de Braga

forecast/meteorology/rcm/rcm-d0.json - Risco de incêndio rural (RCM), de Reduzido a Máximo`

---
3. Apps Scripts associados
Script	Ficheiro	O que faz	Trigger
Registo da folha Dados	OPTEL_Veiculos	Copia cada registo para Canais Radio, Veículos ou Chamada Geral Rede e depois apaga-o da folha Dados.	Caixas "Registar" (onEdit)
Histórico de veículos	OPTEL_Veiculos	Guarda a inoperacionalidade e a reposição na mesma linha do Histórico Veículos, limpa os nomes OP. BSB/CSR e, na reposição, limpa a linha do veículo.	onEdit
Mudança de mês	OPTEL_Veiculos	Transcreve para o novo mês os veículos ainda INOP e identifica o novo mês no Historico CGR. Os dias sem chamada geral ficam em branco.	Temporizado
Encaminhamento	Comando_dashboard	Encaminha os registos da folha de entrada única para a folha correta e ordena-os por data.	onEdit

Projeto Apps Script da folha Dados: `https://script.google.com/home/projects/1cmzMxhzdB5J1UGdOZgtIFTzM4EFtvq_0MTNP7yZS3wwh0_Zru0VZ6v5V/edit`
---

4. Instalação e execução (computador do videowall)
Requisitos: Windows, Python 3, um browser atualizado (Chrome ou Edge), ligação à internet (para ler as folhas) e resolução de `[a preencher]`.
Descarregar o repositório em ZIP e extraí-lo para, por exemplo, `C:\CENTCOM\dashboard`.
Abrir uma linha de comandos nessa pasta e executar:
```
   python -m http.server 8000
   ```
No browser, abrir `http://localhost:8000/index.html`.
Arranque automático: criar um ficheiro `.bat` na pasta Arranque do Windows (`shell:startup`):
```bat
   cd /d C:\CENTCOM\dashboard
   start "" python -m http.server 8000
   timeout /t 5
   start "" msedge --kiosk http://localhost:8000/index.html --edge-kiosk-type=fullscreen
   ```
> A dashboard tem de ser aberta através do servidor local (`http://localhost`) e não com duplo clique no ficheiro, porque o browser bloqueia alguns pedidos de dados em `file://`.
---
5. Configuração
Para alterar estes pontos, procurar no `index.html`:
O quê	Onde
IDs das folhas e gids	`[a preencher — nome da constante]`
Tempo de rotação das páginas	`30seg`
Imagens dos veículos por tipologia	Inseridas diretamente no HTML
Textos do rodapé	`Comando_Dashboard`
Cores do risco de incêndio	`atribui a cor consoante a cor do alerta`
---
6. Estrutura do repositório
```
index.html          Dashboard (todos os quadros + modo ?quadro=)
OcAt.html           Página independente de ocorrências ativas no concelho de Braga
[pasta imagens]/    Imagens dos veículos por tipologia, emblema
README.md           Este ficheiro
```
A página OcAt.html tem documentação própria. Lê os dados através do intermediário Apps Script "Intermediario Fogos" (API Fogos.pt).
---
7. Problemas comuns
Sintoma	Causa provável	Solução
Um quadro aparece vazio	A folha não está partilhada/publicada, ou o gid está errado	Verificar a partilha e os gids da secção 2.
Um agendamento não aparece	Data Inicio vazia ou num formato inválido	Usar o formato de data `dd/mm/aaaa`.
Um agendamento não desaparece	Data Fim ou Hora num formato não reconhecido	Ver os formatos aceites na secção 2.1.
Um veículo não passa ao histórico	OP. BSB ou OP. CSR por preencher	Preencher os dois campos.
Os dados não atualizam	Sem internet, ou o browser em cache	Verificar a ligação e recarregar com Ctrl+F5.
O ecrã fica em branco depois de reiniciar	O servidor local não arrancou	Verificar o `.bat` de arranque.
---
8. Histórico de alterações
Data	Alteração
out/2026	O Plano de Comunicações passa a ter 3 teatros por página, em duas colunas e sem hora. A etiqueta "Evento" passa a "Teatro Operações".
12/10/2026	EPE acrescentada ao quadro Meteorologia.
21/08/2026	Rodapé com avisos de procedimentos.
out/2026	Folha Dados com caixas "Registar".
19/08/2026	Leitura dos veículos e canais a partir do OPTEL_Veiculos.
19/08/2026	Coluna Data Fim e agendamentos de vários dias.
