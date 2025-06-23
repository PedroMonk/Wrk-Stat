# Wrk-Stat

Sprint 1 (Dias 1–21): Planejamento e Estruturação do Projeto
Objetivo principal: Definir escopo detalhado, ambiente de desenvolvimento e blueprint inicial das estruturas de dados e parâmetros em Python.

Revisão detalhada do modelo em AIMMS

Mapear todos os conjuntos, parâmetros, variáveis de decisão e restrições presentes no modelo AIMMS.

Identificar quais partes do modelo precisam ser migradas primeiro (por exemplo, estruturas de dados, parâmetros estáticos, lógica de geração de relatórios).

Configuração do ambiente Python

Escolher a(s) biblioteca(s) de otimização (por exemplo, Pyomo, PuLP ou outra) e garantir licenças necessárias.

Criar ambiente virtual (venv/Conda), instalar dependências iniciais (pandas, NumPy, bibliotecas de solver).

Estruturar repositório Git: criar branches, definir convenções de commits e code style (PEP 8).

Definição da arquitetura de pasta/projetos

Criar uma pasta “data/” para armazenar planilhas de entrada (ou cópias do arquivo AIMMS que contenham dados).

Criar “models/” para scripts que conterão as traduções de cada parte do modelo.

Criar “tests/” para unidades de teste de cada módulo.

Criar “docs/” para documentação técnica (README detalhando como rodar, design do modelo, etc.).

Entregáveis de fim de sprint:

Documento “Blueprint de migração” contendo:

Mapeamento completo dos conjuntos, parâmetros e variáveis do AIMMS para estruturas em Python (por exemplo, DataFrames ou dicionários).

Diagrama simples (ou fluxograma) mostrando como as principais partes do modelo se relacionam.

Repositório Git inicializado com:

Ambiente virtual configurado.

Dependências básicas no requirements.txt.

Estrutura de pastas criada (data/, models/, tests/, docs/).

Exemplo mínimo de leitura de dados:

Script Python que lê as tabelas de entrada (por ex., CSV/Excel) e converte em DataFrame ou dicionário, conforme mapeamento.

Sprint 2 (Dias 22–42): Implementação das Estruturas de Dados e Parâmetros
Objetivo principal: Recriar em Python todas as estruturas de dados (conjuntos e parâmetros) usadas pelo modelo AIMMS, garantindo que sejam carregadas corretamente.

Importação de dados estáticos

Converter dados de entrada (que no AIMMS vinham de planilhas ou parâmetros) para arquivos CSV/Excel, se necessário.

Escrever funções para ler cada tabela e armazenar em estruturas adequadas (pandas.DataFrame, dicionários aninhados ou objetos-pyomo).

Validar consistência: checar se todos os valores carregados batem com o que o AIMMS usava.

Definição de classes ou objetos intermediários (opcional)

Se for vantajoso, criar classes para representar, por exemplo, “Nós” e “Arcos” (em caso de rede), ou “Períodos” e “Recursos”.

Garantir que existam métodos para acessar facilmente dimensão, índice e coordenação de cada parâmetro.

Implementação de parâmetros dinâmicos (caso haja)

Se o modelo AIMMS dependia de cálculos prévios para preencher parâmetros (por exemplo, matriz de distâncias ou tempos), replicar essa lógica em Python.

Escrever scripts que façam o pré‐processamento dos dados (ex.: calcular matrizes de custo, filtrar vazões, etc.).

Testes unitários iniciais

Para cada função de leitura, criar testes que validem um caso de exemplo (por exemplo, comparar a soma de uma coluna com o valor esperado do AIMMS).

Configurar pytest ou unittest para executar esses testes automaticamente.

Entregáveis de fim de sprint:

Módulos Python sob models/data_structures.py (ou similar) que carreguem todo o set e parâmetros do AIMMS.

Testes unitários em tests/test_data_structures.py cobrindo:

Validação de tamanho (quantidade de registros) de cada tabela.

Checagem de valores-chave (por exemplo, somatório de demandas ou distâncias).

Documentação parcial em docs/estrutura_de_dados.md descrevendo como cada parâmetro foi mapeado e carregado.

Sprint 3 (Dias 43–63): Tradução da Lógica de Variáveis de Decisão e Restrições Básicas
Objetivo principal: Implementar em Python a lógica central de variáveis de decisão (por exemplo, binárias, contínuas) e traduzir as restrições básicas do AIMMS para formulas em Pyomo (ou biblioteca escolhida).

Escolha de framework de otimização

Se ainda não definido, confirmar se será Pyomo, PuLP, ou outra. Instalar o solver compatível (GLPK, CBC, Gurobi, CPLEX).

Criar um template de modelo_base.py que instancie um objeto Model (por ex., model = ConcreteModel() no Pyomo).

Definição das variáveis de decisão

Para cada variável AIMMS (binárias, inteiras ou contínuas), criar o equivalente em Python:

python
Copy
model.x = Var(model.I, model.J, domain=Binary)
model.y = Var(model.I, domain=NonNegativeReals)  # Exemplo
Garantir que índices e domínios sejam corretamente configurados.

Implementação das restrições simples

Traduzir cada restrição que não dependa de lógica iterativa (por ex.: demanda ≤ capacidade, somatório de fluxos = 1, etc.).

Exemplos em Pyomo:

python
Copy
def restricao_demanda_rule(model, i):
    return sum(model.x[i,j] for j in model.J) >= model.demand[i]
model.Cons_Demanda = Constraint(model.I, rule=restricao_demanda_rule)
Adição da função objetivo

Recriar o objetivo do AIMMS (minimizar custo, maximizar lucro etc.) conforme fórmula original.

Testar se a função objetivo é reconhecida e calculada corretamente (sem solver ativo).

Validação parcial do modelo

Para um subconjunto de dados de entrada (“toy‐data”), resolver o modelo e comparar com resultados conhecidos do AIMMS (mesmo que seja um modelo reduzido).

Caso algum valor diverja, ajustar índices, domínios ou sinalizações.

Entregáveis de fim de sprint:

Script models/decision_variables_and_constraints.py com:

Definição de variáveis de decisão em Python.

Implementação de pelo menos 80% das restrições (as mais simples).

Tanto em formato de funções rule (se for Pyomo) ou similar, documentadas com comentários sobre a origem no AIMMS.

Testes unitários em tests/test_constraints.py que verificam:

Criação correta de cada variável (checar domínio, forma, tamanho).

Lógica de pelo menos duas restrições simples (para “toy‐data”).

Relatório breve em docs/status_sprint3.md:

Quais restrições foram implementadas.

Quais “corner cases” foram encontrados e como foram resolvidos.

Sprint 4 (Dias 64–84): Cobertura Completa de Restrições Complexas e Testes de Validação
Objetivo principal: Completar a tradução de todas as restrições restantes (inclusive as mais complexas) e validar o modelo completo em casos reais.

Implementação de restrições complexas

Traduzir lógica de restrições que envolvem índices múltiplos, condições “if‐then” ou somas condicionais (por ex., “se a rota visita i e j, então…”, ou restrições de subcircuito em roteirização).

Testar cada restrição isoladamente com cenários controlados.

Refinamento de parâmetros dependentes

Se existirem parâmetros em AIMMS calculados dinamicamente (por ex., parâmetros que mudam conforme decisão), recriar essa lógica em Python via métodos auxiliares antes do solve.

Validação cruzada de resultados

Executar o modelo completo (com solver) em dados de input representativos (ex.: mesmo conjunto utilizado em AIMMS no passado).

Comparar métricas-chave: custo total, utilização de recursos, roteirização final, etc.

Identificar e corrigir desvios maiores que tolerância (por ex., diferenças superiores a 1%).

Análise de performance inicial

Medir tempo de montagem do modelo e de resolução para casos de pequeno e médio porte.

Identificar gargalos (por ex., uso excessivo de loops Python vs. vetorização via NumPy/Pandas).

Entregáveis de fim de sprint:

models/full_model.py contendo:

Todas as variáveis de decisão.

100% das restrições (comentadas e numeradas conforme original AIMMS).

Função objetivo final.

Scripts de validação em tests/test_full_model.py:

Teste “end‐to‐end” que resolve o modelo completo em instância exemplo e checa métricas (por ex., “custo esperado = X”).

Planilha comparativa em docs/resultados_comparacao.xlsx (ou CSV) com colunas:

Parâmetro/resultado (AIMMS vs. Python), diferença absoluta e percentual.

Documento docs/performance_sprint4.md:

Tempo de montagem e solve.

Principais ajustes feitos para performance (p. ex., indexação, uso de sparse matrices, etc.).

Sprint 5 (Dias 85–105): Otimização, Testes de Estresse e Integração
Objetivo principal: Otimizar performance, garantir que o modelo aguente instâncias reais de tamanho pleno e integrar com demais sistemas (por ex., pipelines de dados ou relatórios).

Otimização de performance

Profile do código (cProfile ou similar) para identificar funções/pontos lentos.

Substituir loops pesados por operações vetorizadas (NumPy, Pandas).

Se necessário, usar recursos de solver avançados (definir cortes, parametrizar tolerâncias, warm‐start).

Testes em instâncias de produção

Executar o modelo completo em toda base de dados real (com o mesmo volume que o AIMMS enfrentava) e medir:

Tempo total de execução.

Consumo de memória.

Qualidade da solução (consistência com AIMMS).

Se ficar muito lento, documentar pontos de estrangulamento.

Integração com pipeline de dados

Se houver sistema de ETL (por ex., Airflow, Scripts agendados), criar script/batch que:

Extrai input atualizado (por ex., novas planilhas).

Chama o modelo Python.

Exporta resultados (planilhas, gráficos ou tabela SQL).

Validar que o script roda sem erro ao final do dia (rodar em modo “dry-run” e “production”).

Criação de relatórios automáticos

Gerar outputs para stakeholders: gráficos de sensibilidade, tabelas de resultados, mapas (se for o caso).

Caso use Jupyter, criar notebook de demonstração final (“demo.ipynb”) que mostre passo a passo:

Carregamento de dados → montação do modelo → solve → resultados.

Entregáveis de fim de sprint:

Script otimizado models/optimized_model.py (ou refatoração de full_model.py) com:

Código limpo, modular e comentado.

Benchmarks de performance (tempos medidos anotados nos comentários).

Pipeline de integração em scripts/run_model_pipeline.py que:

Leia inputs, chame solver e salve outputs em formato pré‐definido.

Notebook demo.ipynb (ou PDF gerado) mostrando exemplo completo de uso para um caso real.

Documento docs/intrucoes_de_implantacao.md explicando como:

Instalar dependências.

Rodar o pipeline num servidor Linux/Windows.

Configurar parâmetros de solver (threads, tolerâncias, licenças).

Sprint 6 (Dias 106–126): Documentação Final, Treinamento e Entrega ao Cliente
Objetivo principal: Consolidar toda a documentação, criar manuais e treinar usuários finais ou equipe de manutenção.

Documentação técnica completa

docs/guia_do_usuario.md explicando passo a passo como executar o modelo (pré‐requisitos, comandos, como interpretar resultados).

docs/manual_tecnico.md detalhando todos os módulos Python, classes, funções e fluxo de dados.

docs/FAQ.md com perguntas frequentes antecipadas (por ex., “Como alterar parâmetros?”, “Como ajustar limites de solver?”).

Criação de exemplos de uso

Incluir no repositório uma pasta examples/ com:

Conjuntos de dados de exemplo.

Comando “python run_model_pipeline.py --input examples/exemplo1.xlsx --output examples/saida1.xlsx”.

Treinamento e apresentação

Preparar um slide deck breve (PowerPoint ou PDF) que:

Mostre motivação da migração.

Destaque comparativos de resultados (AIMMS vs. Python).

Demonstre como rodar e interpretar resultados.

Realizar uma reunião (ou gravação) de treinamento de 1 hora para equipe/stakeholders.

Feedback e ajustes finais

Coletar feedback dos usuários que testaram o modelo nos últimos sprints.

Corrigir bugs menores, atualizar documentação conforme dúvidas reais surgirem.

Entregáveis de fim de sprint:

Repositório completo, contendo:

Pasta docs/ com todo o material de documentação.

Pasta examples/ com datasets e scripts de demonstração.

Slides de apresentação (arquivo .pptx ou .pdf).

Registro de feedback e lista de melhorias futuras em docs/roadmap_futuro.md.

Confirmação de aceitação pelo cliente/equipe (por exemplo, e-mail ou ata de reunião).

Resumo dos Sprints
Sprint	Período (dias)	Principais Tarefas	Entregáveis Fundamentais
1	1–21	Planejamento, blueprint de migração, setup inicial	Blueprint de mapeamento, repositório inicial, função mínima de leitura de dados
2	22–42	Estruturas de dados e parâmetros	Módulos de leitura de dados, testes unitários de parâmetros, documentação parcial
3	43–63	Variáveis de decisão e restrições básicas	Script das variáveis+restrições simples, testes unitários parciais, relatório de status
4	64–84	Restrições complexas e validação completa	Modelo completo (full_model.py), testes end-to-end, comparativo AIMMS vs. Python, relatório de performance inicial
5	85–105	Otimização e integração	Script otimizado, pipeline de execução, notebook demo, manual de implantação
6	106–126	Documentação final e treinamento	Documentação completa, exemplos, slides de treinamento, feedback e roadmap futuro
