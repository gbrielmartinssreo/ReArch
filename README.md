# ReArch

> Apoio à recuperação de arquitetura de sistemas legados, ao registro das evidências usadas e à análise do papel da documentação nesse processo.
>
> Este README registra o entendimento inicial do projeto. É um planejamento em construção, não uma descrição de funcionalidades já implementadas. As escolhas metodológicas ainda serão refinadas pelo mapeamento da literatura, por um piloto e pela orientação do TCC.

## De onde vem a ideia

A ideia do ReArch nasce de uma situação que provavelmente vou encontrar na vida profissional: precisar entender e modificar um sistema que já existe há bastante tempo, mas cuja documentação não explica bem como ele funciona ou por que foi organizado daquela maneira.

O código está disponível, mas as explicações podem estar espalhadas entre comentários, READMEs, uma pasta `docs/`, mensagens de commits, issues e páginas externas. Quero juntar essas evidências para compreender a estrutura do sistema e transformar esse entendimento em uma documentação clara e verificável.

Esse problema se conecta ao meu TCC: investigar como a qualidade da documentação e a disponibilidade do histórico de commits influenciam a recuperação da arquitetura de sistemas legados. O ReArch é o instrumento que apoia essa investigação.

## O que queremos construir

O ReArch deve analisar um sistema individualmente, reconstruir uma representação da sua arquitetura, registrar as evidências que sustentam cada elemento e gerar uma proposta de documentação com explicações e diagramas.

Para o TCC, aplicaremos um procedimento comum a dois sistemas e compararemos os resultados. A comparação é uma exigência do estudo, mas não deve ser obrigatória em toda utilização da ferramenta. No futuro, quero poder usar o ReArch em um único repositório do trabalho para entender seus componentes e produzir documentação útil.

Podemos pensar em duas entregas que se complementam:

- **Entendimento do sistema:** componentes, responsabilidades, dependências, decisões identificadas e pontos ainda incertos.
- **Estabilidade ao longo das versões:** como a recuperação se comporta quando o sistema evolui, com indicadores e evidências para comparação.

## A relação com o TCC

O TCC é conduzido em duas partes. A primeira é um **mapeamento sistemático da literatura**, com protocolo próprio, que fundamenta as questões, as técnicas e as métricas. A segunda é um **estudo de caso comparativo** entre dois sistemas legados, conduzido segundo as diretrizes de Runeson e Höst (2009), com o ReArch como apoio.

As questões do mapeamento orientam o estudo de caso:

- **RQ1:** como a qualidade da documentação prévia e a disponibilidade do histórico de commits influenciam o esforço, a precisão e a estabilidade da recuperação arquitetural?
- **RQ2:** quais informações documentais, visões e técnicas fornecem o maior ganho de compreensão na engenharia reversa?
- **RQ3:** qual é o conjunto mínimo de artefatos e informações necessário para recuperar a arquitetura com qualidade aceitável, mesmo após a evolução ou erosão do sistema?

Uma pergunta provisória para o estudo de caso é:

> A qualidade da documentação e a disponibilidade do histórico de commits influenciam o esforço, a precisão e a estabilidade da recuperação da arquitetura em dois sistemas legados de domínio semelhante?

Não vamos assumir de saída que documentação ruim causou bugs ou refatorações. Bugs, refatorações e a qualidade do código não são desfechos do estudo. Mudanças de requisitos, tamanho, equipe e complexidade também podem influenciar os resultados, e o estudo identifica relações, sem afirmar causa.

A escolha dos sistemas ainda está aberta. A preferência é por projetos de domínio semelhante, maturidade comparável e mesma linguagem principal. A linguagem comum facilita a medição, mas não torna os sistemas automaticamente comparáveis. A diferença de qualidade documental precisa ser demonstrada por critérios, e não presumida pela reputação dos projetos.

## Vocabulário

Seguimos as definições de Chikofsky e Cross (1990):

- **Engenharia reversa:** analisar um sistema para identificar seus componentes e inter-relações e criar representações dele em outra forma ou em um nível mais alto de abstração.
- **Redocumentação:** criar ou revisar uma representação do sistema em um nível de abstração equivalente, sem alterar sua implementação.
- **Recuperação de projeto (design recovery):** somar ao que se observa no sistema o conhecimento do domínio, informações externas e dedução, para identificar abstrações de mais alto nível.

Neste projeto, inferir responsabilidades e decisões a partir do código e das fontes é recuperação de projeto. Gerar os novos documentos arquiteturais é redocumentação.

## Olhar para o sistema em momentos diferentes

Uma análise apenas do estado atual não mostra como o sistema chegou até ali, nem se a recuperação se mantém quando ele evolui. Por isso, parte da proposta é trabalhar com **snapshots**: estados do repositório identificados por commits específicos.

Em cada snapshot, queremos reunir o código e a documentação disponíveis naquele momento, extrair a estrutura e aplicar as mesmas medições. Depois, podemos examinar o intervalo entre snapshots para entender como a qualidade da recuperação varia.

Documentação atual não deve ser usada como se estivesse disponível em uma versão antiga. Para fontes externas sem histórico recuperável, será necessário registrar essa limitação.

## Como imaginamos o processo

### 1. Definir o recorte

Antes da coleta, escolheremos o repositório, os snapshots, os diretórios incluídos, as exclusões e a unidade de análise. Precisamos definir se “módulo” significa arquivo, pacote, diretório ou outra entidade, e como componentes arquiteturais serão recuperados a partir dessas unidades.

### 2. Coletar evidências

Pretendemos combinar acesso a repositórios Git locais com coleta pela API do GitHub. As fontes poderão incluir código, documentação, estrutura de pastas, dependências declaradas, scripts de build, mensagens de commits e issues relevantes. Fontes externas serão incorporadas quando acessíveis e úteis ao recorte.

Cada evidência deve preservar sua origem e associação com uma versão. O histórico de commits é tratado como um fator próprio do estudo. Mensagens longas podem conter justificativas importantes, mas não serão tratadas automaticamente como documentação formal de arquitetura.

### 3. Extrair a estrutura

Um extrator compatível com a linguagem identificará entidades e dependências para montar grafos. O Depends é o ponto de partida registrado para investigar o recorte em C/C++; sua adequação às relações e versões escolhidas ainda precisa ser verificada no piloto.

Os indicadores estruturais são calculados por procedimentos definidos, sem pedir à IA que estime valores. Eles servem de **apoio** para descrever a estrutura e a erosão ao longo das versões, e não como resultado do estudo:

| Indicador | O que pretendemos observar |
| --- | --- |
| Dependências de entrada e saída | Acoplamento dos módulos |
| Participação em ciclos | Interdependências estruturais |
| Tamanho de funções e módulos | Contexto para interpretar as outras medidas |

Outras medidas, como complexidade ciclomática e duplicação, podem ser consideradas se forem viáveis e úteis ao recorte. Coesão também, mas depende de uma definição adequada à unidade de análise e ainda não há métrica escolhida. Não pretendemos reduzir a qualidade do código a uma nota única.

### 4. Analisar a documentação

Para investigar a influência da documentação, precisamos caracterizar a qualidade dela com critérios explícitos. A ideia é uma rubrica, não uma nota livre, com critérios como descrição de responsabilidades, interfaces, dependências e justificativas arquiteturais. Cada resultado deve incluir classificação, justificativa, fonte e versão.

Uma escala inicial pode distinguir ausência de explicação encontrada, explicação parcial e explicação suficiente segundo o critério. Essa escala é apenas uma hipótese de trabalho: precisa de fundamentação e teste. “Não encontrado no material examinado” não significa necessariamente “inexistente”, e falha de acesso não deve virar nota baixa.

Um fluxo assistido por IA poderá ajudar a localizar explicações e relacioná-las aos módulos. A IA é um instrumento de apoio, e não o objeto do estudo. Ainda vamos decidir quanto desse trabalho precisa ser autônomo e quanto pode seguir etapas fixas. Parte das classificações será conferida por avaliação humana.

### 5. Recuperar a arquitetura

O grafo fornece relações estruturais, mas ainda precisamos interpretar responsabilidades e propor componentes. Recuperar a arquitetura envolve conectar essas relações ao código, à organização do projeto e às explicações disponíveis.

O resultado deve separar claramente:

- **Observado no código:** entidades e relações extraídas ou verificadas.
- **Declarado nas fontes:** explicações e decisões registradas.
- **Inferido:** interpretações propostas a partir das evidências.

Fluxos de execução e decisões não serão inventados para completar diagramas. Quando as evidências forem insuficientes, a lacuna deve aparecer no resultado.

### 6. Medir esforço, precisão e estabilidade

Para cada sistema e snapshot, registramos:

- **Esforço:** tempo e trabalho necessários para chegar a uma representação defensável. A forma de medir será definida no piloto.
- **Precisão:** concordância da arquitetura recuperada com uma arquitetura de referência, por métrica a escolher a partir do mapeamento.
- **Estabilidade:** variação da qualidade da recuperação entre os snapshots.

### 7. Procurar o conjunto mínimo

Para responder à RQ3, a recuperação pode ser repetida com combinações crescentes de fontes: por exemplo, só código; código e estrutura de pastas; com comentários; com documentação do repositório; com histórico de commits. A comparação mostra a partir de qual combinação a qualidade se torna aceitável. O critério de “aceitável” será definido com base na literatura e no piloto.

### 8. Gerar uma proposta de documentação

Uma entrega prática será um novo conjunto de documentos arquiteturais, por exemplo em `docs/architecture/`, contendo visão geral, componentes, dependências, diagramas, decisões encontradas e limitações. Isso é a redocumentação resultante da recuperação.

Os diagramas poderão ser produzidos em Mermaid, com uma visão geral e visões menores por componente. A saída será uma proposta revisável, identificada pelo commit analisado. Ela ficará separada da documentação original durante o estudo e não será incorporada automaticamente ao repositório.

## Como evitar informação demais

Não queremos analisar todo o histórico e enviar todos os arquivos para uma IA. A proposta é combinar uma coleta estrutural automatizada com investigação textual delimitada.

Um piloto poderá começar com poucos módulos e duas versões de um sistema. Para o estudo comparativo, três snapshots por sistema são uma possibilidade inicial, não uma decisão metodológica fechada.

O processo poderá restringir diretórios, selecionar módulos por regras comuns e reutilizar resultados de conteúdo inalterado. A viabilidade será avaliada antes de fixar o escopo final. Uma possibilidade para conter o trabalho é medir a estabilidade nos snapshots escolhidos e produzir documentação arquitetural completa apenas para o snapshot final.

## Como avaliar o que o ReArch produzir

Precisamos distinguir a avaliação dos sistemas da avaliação da própria ferramenta.

| Objeto | Avaliação pretendida |
| --- | --- |
| Documentação original | Critérios documentais, com evidências |
| Arquitetura recuperada | Precisão, cobertura e fundamentação de componentes e relações |
| Processo de recuperação | Esforço (tempo e trabalho) e estabilidade entre versões |
| Classificação feita pela IA | Concordância com revisão humana em uma amostra definida |
| Documentação gerada | Clareza, utilidade e rastreabilidade das explicações e diagramas |

Será necessário definir a arquitetura de referência por inspeção do código e, quando possível, revisão de alguém familiarizado com o sistema. A documentação de entrada não pode ser o único gabarito, pois também pode conter erros ou estar desatualizada.

O consumo de tokens pode ser registrado para avaliar viabilidade operacional, mas não substitui a medição de esforço do estudo nem a validação da arquitetura.

## Reaplicar em outros sistemas

Queremos que a ferramenta analise um sistema por vez e produza resultados em um formato comum. A comparação receberá resultados de análises já executadas, como uma etapa separada.

O método manterá definições de métricas, critérios documentais e formato das evidências. A configuração de cada projeto indicará fontes, linguagem, extrator, snapshots e escopo.

Isso permitiria usar o ReArch em um único sistema legado no trabalho, sem precisar encontrar outro projeto para comparação. Suporte a várias linguagens é uma possibilidade futura; o estudo inicial pode continuar restrito a uma linguagem para facilitar a validação.

Também queremos registrar versões de ferramentas, configurações, instruções e respostas da IA. Isso permite auditar o procedimento, mesmo que repetir uma geração não produza exatamente o mesmo texto.

## O que ainda precisamos decidir

- O que o mapeamento sistemático vai indicar sobre técnicas, visões e métricas de precisão.
- Quais serão os dois sistemas e qual será o escopo comparável.
- Qual unidade será chamada de módulo e como serão identificados os componentes.
- Como selecionar snapshots e acompanhar arquivos ou módulos renomeados, divididos ou fundidos.
- Como medir o esforço e como definir “qualidade aceitável” de recuperação.
- Como avaliar documentação histórica e tratar fontes indisponíveis.
- Como construir e validar a arquitetura de referência.
- Qual será o papel da IA, o modelo utilizado e o orçamento das análises.

O próximo passo é confrontar esse entendimento com a literatura do mapeamento e executar um piloto pequeno. Queremos descobrir cedo se as evidências disponíveis permitem fazer a análise pretendida, antes de ampliar a coleta ou fechar a implementação.

## Onde queremos chegar

Queremos sair de um repositório difícil de compreender para uma visão explicada da sua arquitetura, acompanhada de fontes, diagramas e dúvidas explícitas. Para o TCC, esse processo deve fornecer dados para comparar esforço, precisão e estabilidade da recuperação em dois sistemas legados e para investigar o papel da documentação.

O ReArch será o instrumento que reúne essas partes: coleta, análise estrutural, interpretação de evidências e documentação recuperada. O estudo comparativo será uma aplicação desse instrumento, e o uso profissional em sistemas individuais será outra.
