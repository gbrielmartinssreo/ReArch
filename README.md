# ReArch

> Recuperação de arquitetura, análise da evolução estrutural e geração de documentação para sistemas legados.
>
> Este README registra o entendimento inicial do projeto. É um planejamento em construção, não uma descrição de funcionalidades já implementadas. As escolhas metodológicas ainda serão refinadas pela leitura de artigos, por um piloto e pela orientação do TCC.

## De onde vem a ideia

A ideia do ReArch nasce de uma situação que provavelmente vou encontrar na vida profissional: precisar entender e modificar um sistema que já existe há bastante tempo, mas cuja documentação não explica bem como ele funciona ou por que foi organizado daquela maneira.

O código está disponível, mas as explicações podem estar espalhadas entre comentários, READMEs, uma pasta `docs/`, mensagens de commits, issues e páginas externas. Quero juntar essas evidências para compreender a estrutura do sistema e transformar esse entendimento em uma documentação clara e verificável.

Ao mesmo tempo, esse problema se conecta ao meu TCC: investigar como a qualidade da documentação se relaciona com a evolução e a qualidade estrutural de sistemas legados. A recuperação de arquitetura será uma forma de compreender esses sistemas e apoiar a investigação.

## O que queremos construir

O ReArch deve conseguir analisar um sistema individualmente, reconstruir uma representação da sua arquitetura, calcular indicadores estruturais e gerar uma proposta de documentação com explicações e diagramas.

Para o TCC, aplicaremos um procedimento comum a dois sistemas e compararemos os resultados. A comparação é uma exigência do estudo, mas não deve ser obrigatória em toda utilização da ferramenta. No futuro, quero poder usar o ReArch em um único repositório do trabalho para entender seus componentes e produzir documentação útil.

Podemos pensar em duas entregas que se complementam:

- **Entendimento do sistema:** componentes, responsabilidades, dependências, decisões identificadas e pontos ainda incertos.
- **Análise da evolução:** mudanças na estrutura e na documentação ao longo de versões selecionadas, com indicadores e evidências para comparação.

## A relação com o TCC

O tema continua sendo sistemas legados, recuperação de arquitetura e o papel da documentação. Queremos investigar indícios de que lacunas documentais estejam relacionadas a dificuldades de manutenção, retrabalho ou deterioração estrutural.

Uma pergunta provisória é:

> Como diferenças na qualidade da documentação se relacionam com a evolução da qualidade estrutural do código em dois sistemas legados de domínio semelhante, analisados com apoio da recuperação de arquitetura?

Não vamos assumir de saída que documentação ruim causou bugs ou que toda refatoração representa um problema. As métricas mostram mudanças; o histórico e as demais evidências ajudam a investigar explicações. Mudanças de requisitos, tamanho, equipe e complexidade também podem influenciar os resultados.

A escolha dos sistemas ainda está aberta. A preferência é por projetos de domínio semelhante, maturidade comparável e mesma linguagem principal. A linguagem comum facilita a medição, mas não torna os sistemas automaticamente comparáveis. A diferença de qualidade documental precisa ser demonstrada por critérios, e não presumida pela reputação dos projetos.

## Olhar para o sistema em momentos diferentes

Uma análise apenas do estado atual não mostra como o sistema chegou até ali. Por isso, uma parte importante da proposta é trabalhar com **snapshots**: estados do repositório identificados por commits específicos.

Em cada snapshot, queremos reunir o código e a documentação disponíveis naquele momento, extrair a estrutura e aplicar as mesmas medições. Depois, podemos examinar o intervalo entre snapshots para entender mudanças relevantes.

Por exemplo: um módulo ganhou dependências, mas sua documentação não mudou. Isso não prova que houve um problema, porém oferece um caso concreto para investigar. Quais funcionalidades foram adicionadas? Houve justificativa? As interfaces mudaram? Existem correções ou discussões relacionadas?

Documentação atual não deve ser usada como se estivesse disponível em uma versão antiga. Para fontes externas sem histórico recuperável, será necessário registrar essa limitação.

## Como imaginamos o processo

### 1. Definir o recorte

Antes da coleta, escolheremos o repositório, os snapshots, os diretórios incluídos, as exclusões e a unidade de análise. Precisamos definir se “módulo” significa arquivo, pacote, diretório ou outra entidade, e como componentes arquiteturais serão recuperados a partir dessas unidades.

### 2. Coletar evidências

Pretendemos combinar acesso a repositórios Git locais com coleta pela API do GitHub. As fontes poderão incluir código, documentação, estrutura de pastas, dependências declaradas, scripts de build, mensagens de commits e issues relevantes. Fontes externas serão incorporadas quando acessíveis e úteis ao recorte.

Cada evidência deve preservar sua origem e associação com uma versão. Mensagens longas de commits podem conter justificativas importantes, mas não serão tratadas automaticamente como documentação formal de arquitetura.

### 3. Extrair estrutura e calcular métricas

Um extrator compatível com a linguagem identificará entidades e dependências para montar grafos. O Depends é o ponto de partida registrado para investigar o recorte em C/C++; sua adequação às relações e versões escolhidas ainda precisa ser verificada no piloto.

As métricas serão calculadas por procedimentos definidos, sem pedir à IA que estime valores estruturais. Entre as candidatas estão:

| Indicador | O que pretendemos observar |
| --- | --- |
| Dependências de entrada e saída | Acoplamento dos módulos |
| Participação em ciclos | Interdependências estruturais |
| Complexidade ciclomática | Complexidade dos caminhos de execução nas funções |
| Tamanho de funções e módulos | Contexto para interpretar outras medidas |
| Duplicação, se viável | Repetição de código no escopo analisado |

Nem todas essas medidas saem de um grafo de dependências. Complexidade e duplicação podem exigir ferramentas adicionais. Coesão também poderá ser considerada, mas depende de uma definição adequada à unidade de análise; ainda não há uma métrica escolhida.

Não pretendemos reduzir qualidade do código a uma nota única. As medidas precisam ser interpretadas em conjunto, considerando tamanho, granularidade e contexto.

### 4. Analisar documentação com apoio de IA

Um agente ou fluxo assistido por IA poderá localizar explicações, relacioná-las aos módulos e aplicar critérios documentais. Ainda vamos decidir quanto dessa investigação precisa ser autônoma e quanto pode seguir etapas fixas.

A ideia não é pedir uma nota livre para a documentação. Queremos uma rubrica com critérios explícitos, como descrição de responsabilidades, interfaces, dependências e justificativas arquiteturais. Cada resultado deve incluir classificação, justificativa, fonte e versão.

Uma escala inicial pode distinguir ausência de explicação encontrada, explicação parcial e explicação suficiente segundo o critério. Essa escala é apenas uma hipótese de trabalho: precisa de fundamentação e teste. “Não encontrado no material examinado” não significa necessariamente “inexistente”, e falha de acesso não deve virar nota baixa.

Parte das classificações será conferida por avaliação humana. A IA ajuda a organizar e interpretar as evidências, mas suas respostas também precisam ser avaliadas.

### 5. Recuperar a arquitetura

O grafo fornece relações estruturais, mas ainda precisamos interpretar responsabilidades e propor componentes. Recuperar a arquitetura envolve conectar essas relações ao código, à organização do projeto e às explicações disponíveis.

O resultado deve separar claramente:

- **Observado no código:** entidades e relações extraídas ou verificadas.
- **Declarado nas fontes:** explicações e decisões registradas.
- **Inferido:** interpretações propostas a partir das evidências.

Fluxos de execução e decisões não serão inventados para completar diagramas. Quando as evidências forem insuficientes, a lacuna deve aparecer no resultado.

### 6. Comparar e investigar mudanças

Com os resultados organizados por sistema e snapshot, poderemos comparar tendências documentais e estruturais. Mudanças relevantes poderão motivar uma análise mais detalhada de arquivos, commits e issues do período.

Os critérios de escolha desses casos precisam ser registrados. Também queremos observar casos estáveis ou que contrariem nossa expectativa, em vez de selecionar apenas exemplos de piora.

### 7. Gerar uma proposta de documentação

Uma entrega prática será um novo conjunto de documentos arquiteturais, por exemplo em `docs/architecture/`, contendo visão geral, componentes, dependências, diagramas, decisões encontradas e limitações.

Os diagramas poderão ser produzidos em Mermaid, com uma visão geral e visões menores por componente. O objetivo é permitir que outra pessoa entenda o sistema e consiga conferir de onde vieram as explicações.

Essa saída será uma proposta revisável, identificada pelo commit analisado. Ela ficará separada da documentação original durante o estudo e não será incorporada automaticamente ao repositório. Gerar documentação também não significa melhorar, por si só, as métricas do código.

## Como evitar informação demais

Não queremos analisar todo o histórico e enviar todos os arquivos para uma IA. A proposta é combinar uma coleta estrutural automatizada com investigação textual delimitada.

Um piloto poderá começar com poucos módulos e duas versões de um sistema. Para o estudo comparativo, três snapshots por sistema são uma possibilidade inicial, não uma decisão metodológica fechada.

O processo poderá restringir diretórios, selecionar módulos por regras comuns, reutilizar resultados de conteúdo inalterado e aprofundar somente intervalos relevantes. A viabilidade será avaliada antes de fixar o escopo final.

Uma possibilidade para conter o trabalho é medir a evolução nos snapshots escolhidos e produzir documentação arquitetural completa apenas para o snapshot final.

## Como avaliar o que o ReArch produzir

Precisamos distinguir a avaliação dos sistemas da avaliação da própria ferramenta.

| Objeto | Avaliação pretendida |
| --- | --- |
| Código dos sistemas | Métricas estruturais e sua evolução |
| Documentação original | Critérios documentais com evidências |
| Classificação feita pela IA | Concordância com revisão humana em uma amostra definida |
| Arquitetura recuperada | Correção, cobertura e fundamentação de componentes e relações |
| Documentação gerada | Clareza, utilidade e rastreabilidade das explicações e diagramas |

Será necessário definir uma referência de avaliação por inspeção do código e, quando possível, revisão de alguém familiarizado com o sistema. A documentação de entrada não pode ser o único gabarito, pois também pode conter erros ou estar desatualizada.

Tempo de execução e consumo de tokens podem ser registrados para avaliar viabilidade operacional, mas não substituem as métricas de qualidade do código nem a validação da arquitetura.

## Reaplicar em outros sistemas

Queremos que a ferramenta analise um sistema por vez e produza resultados em um formato comum. A comparação receberá resultados de análises já executadas, como uma etapa separada.

O método manterá definições de métricas, critérios documentais e formato das evidências. A configuração de cada projeto indicará fontes, linguagem, extrator, snapshots e escopo.

Isso permitiria usar o ReArch em um único sistema legado no trabalho, sem precisar encontrar outro projeto para comparação. Suporte a várias linguagens é uma possibilidade futura; o estudo inicial pode continuar restrito a uma linguagem para facilitar a validação.

Também queremos registrar versões de ferramentas, configurações, instruções e respostas da IA. Isso permite auditar o procedimento, mesmo que repetir uma geração não produza exatamente o mesmo texto.

## O que ainda precisamos decidir

- Quais artigos fundamentarão a recuperação, as métricas e os critérios documentais.
- Quais serão os dois sistemas e qual será o escopo comparável.
- Qual unidade será chamada de módulo e como serão identificados os componentes.
- Como selecionar snapshots e acompanhar arquivos ou módulos renomeados, divididos ou fundidos.
- Quais métricas serão efetivamente adotadas e quais ferramentas as calcularão.
- Como avaliar documentação histórica e tratar fontes indisponíveis.
- Como validar a arquitetura e a documentação produzidas.
- Qual será o papel do agente, o modelo utilizado e o orçamento das análises.

O próximo passo é confrontar esse entendimento com a literatura e executar um piloto pequeno. Queremos descobrir cedo se as evidências disponíveis permitem fazer a análise pretendida, antes de ampliar a coleta ou fechar a implementação.

## Onde queremos chegar

Queremos sair de um repositório difícil de compreender para uma visão explicada da sua arquitetura, acompanhada de fontes, diagramas e dúvidas explícitas. Para o TCC, esse processo também deve fornecer dados para comparar a evolução de dois sistemas e investigar o papel da documentação.

O ReArch será o instrumento que reúne essas partes: coleta, análise estrutural, interpretação de evidências e documentação recuperada. O estudo comparativo será uma aplicação desse instrumento, e o uso profissional em sistemas individuais será outra.
