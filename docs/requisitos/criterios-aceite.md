\# Critérios de Aceite — OficinaPro



> \*\*Documento de trabalho — sujeito à validação do grupo e do professor.\*\*

>

> Os critérios abaixo representam condições propostas para considerar uma funcionalidade atendida. Eles deverão ser revisados conforme as decisões do grupo e a definição final do MVP.



\---



\# 1. Objetivo



Os critérios de aceite têm como objetivo estabelecer condições claras para verificar se uma funcionalidade atende ao comportamento esperado antes de ser considerada concluída.



Uma funcionalidade só deverá ser considerada concluída quando:



\* atender aos critérios definidos;

\* respeitar as regras de negócio aplicáveis;

\* não apresentar erros conhecidos que impeçam seu uso;

\* possuir os testes definidos para sua implementação;

\* passar pelas validações estabelecidas pelo grupo.



\---



\# 2. Cadastro de Clientes



\## CA-CLI-001 — Cadastrar cliente



A funcionalidade será considerada aceita quando:



\* \[ ] for possível informar os dados definidos como obrigatórios;

\* \[ ] os dados forem validados;

\* \[ ] um cliente válido for persistido;

\* \[ ] o cliente puder ser posteriormente consultado;

\* \[ ] o sistema impedir duplicidades conforme regra definida.



\*\*Status:\*\* 🟡 Em discussão



\---



\## CA-CLI-002 — Consultar cliente



A funcionalidade será considerada aceita quando:



\* \[ ] for possível localizar um cliente;

\* \[ ] os dados cadastrados forem exibidos corretamente;

\* \[ ] os veículos associados puderem ser identificados;

\* \[ ] o sistema tratar corretamente cliente inexistente.



\*\*Status:\*\* 🟡 Em discussão



\---



\## CA-CLI-003 — Atualizar cliente



A funcionalidade será considerada aceita quando:



\* \[ ] dados permitidos puderem ser alterados;

\* \[ ] validações forem aplicadas;

\* \[ ] os dados atualizados forem persistidos;

\* \[ ] a alteração puder ser consultada posteriormente.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 3. Cadastro de Veículos



\## CA-VEI-001 — Cadastrar veículo



A funcionalidade será considerada aceita quando:



\* \[ ] for possível cadastrar um veículo;

\* \[ ] o veículo estiver associado a um cliente;

\* \[ ] os campos obrigatórios forem validados;

\* \[ ] o sistema impedir duplicidade conforme regra definida;

\* \[ ] o veículo puder ser consultado posteriormente.



\*\*Status:\*\* 🟡 Em discussão



\---



\## CA-VEI-002 — Consultar veículo



A funcionalidade será considerada aceita quando:



\* \[ ] for possível localizar o veículo;

\* \[ ] seus dados forem exibidos;

\* \[ ] seu cliente associado for identificado;

\* \[ ] seu histórico de manutenção puder ser acessado quando aplicável.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 4. Ordem de Serviço



\## CA-OS-001 — Abrir OS



A funcionalidade será considerada aceita quando:



\* \[ ] for possível selecionar cliente e veículo;

\* \[ ] os dados obrigatórios forem preenchidos;

\* \[ ] a OS receber um identificador único;

\* \[ ] a OS receber o status inicial definido;

\* \[ ] a OS puder ser consultada posteriormente.



\*\*Status:\*\* 🟡 Em discussão



\---



\## CA-OS-002 — Consultar OS



A funcionalidade será considerada aceita quando:



\* \[ ] for possível localizar uma OS;

\* \[ ] cliente e veículo forem exibidos;

\* \[ ] o status atual for exibido;

\* \[ ] diagnóstico, orçamento e informações relacionadas forem exibidos quando existentes.



\*\*Status:\*\* 🟡 Em discussão



\---



\## CA-OS-003 — Atualizar status da OS



A funcionalidade será considerada aceita quando:



\* \[ ] somente transições permitidas forem executadas;

\* \[ ] o status atual for atualizado corretamente;

\* \[ ] alterações relevantes forem registradas;

\* \[ ] usuários sem permissão não possam executar alterações restritas.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 5. Diagnóstico



\## CA-DIA-001 — Registrar diagnóstico



A funcionalidade será considerada aceita quando:



\* \[ ] o diagnóstico puder ser associado a uma OS;

\* \[ ] for possível registrar sua descrição;

\* \[ ] o responsável puder ser identificado quando essa regra for adotada;

\* \[ ] o diagnóstico puder ser consultado posteriormente.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 6. Orçamento



\## CA-ORC-001 — Criar orçamento



A funcionalidade será considerada aceita quando:



\* \[ ] serviços puderem ser adicionados;

\* \[ ] peças puderem ser adicionadas;

\* \[ ] quantidades puderem ser informadas;

\* \[ ] valores forem calculados corretamente;

\* \[ ] descontos sejam aplicados conforme regra;

\* \[ ] o total seja calculado corretamente;

\* \[ ] o orçamento fique



