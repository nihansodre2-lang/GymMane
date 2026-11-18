# Contexto: app de treino do Jonas sobre o GymMane

Este documento serve para abrir no Claude Code junto com o repositório GymMane. Ele resume o que o Jonas pediu durante a conversa no Claude e mostra o que o GymMane já tem, o que falta e por onde começar.

## 1. O que o Jonas quer

Objetivo pessoal: ganhar massa muscular, com foco em "V" (costas largas e ombro lateral, cintura visualmente mais fina), comendo bem.

Pedidos que ele fez ao longo da conversa, em ordem:

1. **Treino inspirado no "treino em V" do Renato Cariani.** O programa V-Shape do Cariani é pago e não mostra os exercícios, então o plano é uma adaptação da divisão que o Cariani divulga abertamente, com reforço de costas e elevação lateral. O plano está em `plano-treino-em-v.json`.
2. **Dieta de hipertrofia** com superávit calórico, calculada pelo peso, altura e idade, em 5 refeições, com comida barata do Norte (ovo, frango, arroz, feijão, macaxeira, banana, cuscuz, aveia, pão).
3. **App de check-in no celular Android**, para marcar treinos e acompanhar a evolução.
4. **Os dados precisam ficar salvos.** O arquivo HTML offline não guardou os dados no Chrome do Android dele. Isso foi o que mais incomodou.
5. **Visual escuro e estiloso**, no estilo das referências que ele mandou: fundo violeta escuro, gradiente roxo para rosa, anéis de progresso, barra de navegação inferior.
6. **Check-in rápido, sem digitar à toa:**
   - não digitar repetições em toda série, porque o plano já diz quantas são;
   - a carga já vem preenchida e só se edita num menu discreto (três pontinhos);
   - opção de pular um exercício ou o treino do dia.
7. **Carga inicial em todos os exercícios**, calculada pelo peso corporal com as faixas do guia em PDF (tabela na seção 4), e não só no exercício que ele digitou.
8. **Progressão automática e lembrete periódico:** de tempos em tempos (padrão 2 semanas, ajustável), o app revisa as cargas e propõe subir ou descer. Se ele estiver usando mais carga que o recomendado, o app passa a seguir o histórico dele e ajusta as estimativas dos outros exercícios.

## 2. O que já foi feito (versão provisória)

Existe uma versão web do app, publicada como página no Claude do Jonas (https://claude.ai/artifact/BVhKLhFwh2hXbQDGo1gE97). Ela já faz:

- check-in por toque nas séries (1, 2, 3, 4), com cronômetro de descanso automático;
- carga estimada pelo peso corporal em todos os exercícios, editável pelos três pontinhos;
- aviso de subir carga depois de 2 treinos completos com a mesma carga, e de descer depois de 2 treinos com séries incompletas;
- card "Revisão de carga" a cada N semanas, com "Aplicar tudo";
- recorde, nota por exercício que reaparece no treino seguinte, tempo de treino;
- dieta calculada (Mifflin-St Jeor, 2 g de proteína por kg, 1 g de gordura por kg, resto em carboidrato, superávit de 250, 350 ou 500 kcal);
- gráfico de peso corporal e de carga por exercício.

Limites dela: não manda notificação, não é um app instalado de verdade, e o salvamento depende do Claude.

## 3. O que o GymMane já resolve

O GymMane (github.com/InlitX/GymMane) é um app Flutter offline para Android, já traduzido para português. Pelo código:

| Pedido do Jonas | No GymMane |
|---|---|
| App Android instalado, dados salvos no aparelho | Sim. Armazenamento local, backup em arquivo e backup automático (`services/local_store.dart`, `auto_backup.dart`). Publicado no F-Droid e no GitHub. |
| Importar o plano pronto | Sim. Aceita JSON com rotinas, dias da semana, séries, faixa de reps, carga e descanso (`services/plan_share.dart`, `screens/plan_import_sheet.dart`). |
| Progressão automática | Sim. Regras por exercício: manual, linear, **dupla progressão**, volume, AMRAP, com deload depois de N falhas (`models/progression.dart`). |
| Lembretes | Sim. Notificações de treino e de progresso (`services/train_reminder.dart`, `progress_reminder.dart`). |
| Cronômetro de descanso | Sim, com alarme (`services/rest_alarm.dart`). |
| Recordes e gráficos | Sim (`screens/progress_screen.dart`, `state/stats_state.dart`). |
| Dieta | Sim, módulo de nutrição (`screens/nutrition/`), com catálogo de alimentos em inglês. |
| Tema escuro | Sim, mas com a paleta do próprio app (tons de marrom e cobre), não roxo. |

## 4. O que falta para ficar como o Jonas pediu

Na ordem que eu sugiro fazer:

1. **Testar sem mexer em código.** Instalar o GymMane, importar `plano-treino-em-v.json` e usar uma semana. Pode ser que só isso resolva.
2. **Carga inicial pelo peso corporal.** Ao importar ou ao abrir um exercício sem carga, preencher com `fração × peso corporal`, arredondado (meio quilo abaixo de 10 kg, 1 kg acima). Faixas usadas (começar pelo mínimo):

   | Exercício | Fração do peso | Exercício | Fração do peso |
   |---|---|---|---|
   | Supino reto | 0,40 a 0,55 | Desenvolvimento c/ halteres (cada) | 0,15 a 0,22 |
   | Supino inclinado c/ halteres (cada) | 0,15 a 0,22 | Elevação lateral (cada) | 0,04 a 0,08 |
   | Agachamento | 0,50 a 0,70 | Rosca direta barra | 0,20 a 0,30 |
   | Leg press 45° | 1,0 a 1,5 | Tríceps na polia | 0,25 a 0,40 |
   | Puxada frontal | 0,50 a 0,65 | Stiff | 0,40 a 0,60 |
   | Remada curvada | 0,35 a 0,50 | Cadeira extensora | 0,40 a 0,60 |
   | Remada baixa | 0,40 a 0,55 | Mesa flexora | 0,25 a 0,40 |
   | Panturrilha em pé | 0,60 a 1,00 | Panturrilha sentado | 0,40 a 0,60 |
   | Peck deck | 0,20 a 0,30 | Crossover (cada lado) | 0,10 a 0,20 |
   | Abdominal na polia | 0,25 a 0,40 | Afundo (cada halter) | 0,10 a 0,15 |
   | Pulldown braço reto | 0,20 a 0,30 | Encolhimento (cada) | 0,20 a 0,30 |
   | Crucifixo inverso | 0,10 a 0,15 | Rosca martelo (cada) | 0,08 a 0,12 |
   | Tríceps testa | 0,15 a 0,25 | Hiperextensão lombar | peso do corpo |

   As 15 primeiras vêm de referências comuns de iniciante. As outras 11 são estimativas, sem fonte publicada. Se o Jonas ajustar 2 ou mais exercícios, calibrar as estimativas restantes pela mediana da razão `carga real ÷ carga estimada`, limitada entre 0,8 e 1,25.
3. **Revisão de carga periódica.** Um aviso a cada N semanas (padrão 2) que lista os exercícios para subir ou descer, com "Aplicar tudo". A lógica de subir e descer já existe em `progression.dart`; falta o lembrete agrupado e a tela de revisão.
4. **Check-in mais curto, se o fluxo padrão do GymMane pedir reps em toda série.** Marcar a série com um toque assumindo as reps do plano, e editar só quando for diferente.
5. **Tema roxo e rosa.** Ajustar as cores em `lib/theme/`.
6. **Dieta com comida brasileira.** Acrescentar ao catálogo alimentos da tabela TACO (macaxeira, cuscuz de milho, pão francês, queijo minas, açaí sem açúcar, farinha d'água) e os alvos de macro descritos na seção 2.

## 5. Regras do repositório que importam

- **Licença GPLv3 com termo de atribuição.** Qualquer versão modificada precisa mostrar "Based on GymMane by InlitX" na tela Sobre. Se o app for distribuído para outras pessoas (vendido ou não), o código modificado também tem que ser disponibilizado sob GPLv3. Para uso só do Jonas, basta manter a atribuição.
- **O projeto original não aceita nada que use internet ou contas.** Isso só importa se vocês quiserem mandar as mudanças de volta ao projeto. No fork de vocês, tanto faz, mas o app fica mais simples sem rede.
- **Estilo da casa:** sem comentários no código; todo texto que aparece na tela passa por `t.<chave>` e pelos arquivos `.arb` (um teste verifica isso). Ao criar um texto novo, colocar pelo menos em `app_en.arb` e `app_pt.arb`.
- **Para compilar:** Flutter 3.41+ (Dart 3.11+) e Android SDK. Rodar `flutter pub get`, `flutter test` (350+ testes) e `flutter build apk --release`.

## 6. Dados do Jonas

O que o Jonas registrar na versão web não passa sozinho para o GymMane. Se ele já tiver histórico lá, dá para escrever um conversor do JSON da "Cópia de segurança" (aba Progresso) para o formato de importação do GymMane.
