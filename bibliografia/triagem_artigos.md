# Triagem de Artigos (Kitchenham: seleção por título, abstract, conclusão)

**Início:** 9 set 2026
**Última atualização:** 18 set 2026
**Fonte dos candidatos:** `busca_artigos` (strings A, B, C, D)
**Total de candidatos únicos:** 53 (51 das strings A a D, mais AgentCare-Guard na re-execução da string A, mais Moisei & Mocanu, que já constava do projeto antes das buscas)

---

## Critérios

### Inclusão (CI)
- **CI1** Publicação entre 2021 e 2026
- **CI2** Foco explícito em vulnerabilidades, arquitetura ou implementação de segurança em APIs FHIR / SMART on FHIR
- **CI3** Segurança de APIs no domínio de saúde conectada / interoperabilidade clínica
- **CI4** Menciona HIPAA, conformidade regulatória ou requisitos de segurança em saúde
- **CI5** Descreve vulnerabilidades exploráveis em implementação real (não puramente teórico)
- **CI6** Inglês ou português

### Exclusão (CE)
- **CE1** FHIR restrito a interoperabilidade semântica / ontologias, sem análise de segurança
- **CE2** Segurança de infraestrutura de rede física/perimetral, sem foco em APIs REST
- **CE3** Resumo expandido, pôster, preprint sem revisão por pares, ou trabalho sem detalhamento metodológico
- **CE4** Duplicata entre bases
- **CE5** Puramente teórico, sem validação
- **CE6** Veículo sem revisão por pares verificável, ou periódico com indícios de predatório

### Distinção entre estudo primário e literatura de apoio

Kitchenham separa estudos primários (selecionados pelo protocolo, contados no corpus, extraídos na matriz) de literatura de apoio (citada na fundamentação teórica, fora da contagem). Um trabalho pode falhar em CI2/CI5 e ainda assim ser útil como apoio. A seção "Literatura de apoio" abaixo registra esses casos.

---

## Sobre o CE6: por que é necessário

Das 53 entradas, cerca de 18 vêm de periódicos com indícios fortes de prática predatória (taxa de publicação paga, revisão por pares ausente ou simbólica, escopo indiscriminado, sites sem corpo editorial verificável). Sinais observados:

- Domínios genéricos de "International Journal of..." sem DOI de editora reconhecida: `ijrt.org`, `ijctece.com`, `ijeetr.com`, `ijarcst.org`, `ijhit.info`, `ijrpetm.com`, `internationaljournalssrp.org`, `iadier-academy.org`, `tpmap.org`
- Títulos que empilham buzzwords sem recorte
- Autor único em artigos que prometem framework completo + validação + otimização
- Datas de publicação à frente do ciclo editorial normal

Incluir esses artigos enfraquece o trabalho. O corte é defensável e vale documentar no protocolo como decisão metodológica.

Para verificar um caso duvidoso, checar se o DOI é de editora reconhecida (IEEE, ACM, Springer, Elsevier, Sage, Wiley, MDPI, Frontiers), e conferir se o corpo editorial tem afiliações rastreáveis.

---

## Camada 1: Estudos primários confirmados

Lidos na íntegra, critérios atendidos, veículo confiável. Contam no corpus.

| # | Referência | Veículo | Ano | Método | Status | O que fornece |
|---|---|---|---|---|---|---|
| 1 | OPIE, C. A. *Exploring security vulnerabilities in FHIR server implementations: a case study on IBM's FHIR Server* | Dissertação, Univ. Hawaii | 2024 | Pentest em laboratório (BurpSuite, ZAP, Wazuh) | Lido (cap. 1-3, 5, 6; cap. 4 pendente) | 7 vulnerabilidades em 1 servidor com SMART configurado. Gaps de **design**: API4, API8 client-side (XSS, clickjacking, PRSSI, referrer). Discussão do "regulatory blind spot" HIPAA/Cures Act (cap. 6.2.2) |
| 2 | BRÜGGEMANN, N. et al. *Measuring Healthcare Data Leaks and Security Flaws at Internet Scale* | IEEE EuroS&P | 2026 | Scan IPv4/IPv6 + honeypot 9 meses | Lido (íntegra) | 1.477 endpoints FHIR; 242 expõem /Patient sem auth; 66,6% sem TLS; 40 com CVE 9.8. Tabela 3: SMART/OAuth quando presente quase não vaza; 58% não anunciam auth. Gaps de **adoção**: API2, API8, API9. Código aberto, método replicável |
| 3 | THARAKA, Y. M. S. et al. *Benchmarking Security Features in Open-Source Healthcare Software Systems* | ACM AMASS '26 | 2026 | Auditoria de documentação, 5 EMRs | Lido (íntegra) | Tabela 4: GNU Health usa Basic auth na API; nenhum dos 5 cifra ou assina payload FHIR; conformidade depende de configuração, não de default. **Ressalva:** não testa, não explora. Falha CI5 no sentido estrito; entra por validar presença/ausência de controle |
| 4 | RANATUNGE, R.; KARUNAPEMA, P. K. *Secure FHIR Transactions Based on the SMART on FHIR Approach in a Health Data Exchange* | MEDINFO 2025 (IOS Press, SHTI) | 2025 | Implementação em produção (MoH Sri Lanka) + testes automatizados de CRUD por papel | Lido (íntegra) | FHIR Auth: servidor OAuth2 SMART backend services, tokens de 15 min, reverse proxy para múltiplos FHIR servers, open source. **Único do corpus que implementa SMART server-side.** Granularidade por recurso resolvida (lab edita DiagnosticReport, não Patient): API1/API3/API5 e minimum necessary na prática. Revisão deles não achou implementações server-side compliant. Explica por que adoção falha: complexidade do guia + falta de recursos. **Ressalva:** não passa CI5 (implementação, não vulnerabilidade); zero OWASP/HIPAA/GDPR; 3 refs Wikipedia; 5 páginas |

### Triangulação por método

| Fonte | Escopo | Profundidade | O que mostra |
|---|---|---|---|
| Opie | 1 servidor | Alta | O que falha com SMART bem configurado (gap de design) |
| Brüggemann | 1.477 endpoints | Baixa | Quantos não configuram SMART (gap de adoção, escala) |
| Tharaka | 5 EMRs | Média | Quais controles existem no software (presença) |
| Ranatunge | 1 implantação nacional | Média | O que SMART cobre quando implementado por completo, e por que raramente é |

---

## Camada 1b: Candidatos a primário (abstract aprovado, leitura completa pendente)

Vazia em 16 set. Os quatro candidatos anteriores foram resolvidos: López Martínez e Nowrozy lidos na íntegra e movidos para apoio; Šafran e Demurjian excluídos por CE6 e CI1.

---

## Camada 2: Talvez (ler abstract + conclusão antes de decidir)

Vazia em 16 set. Ranatunge lido na íntegra e promovido a primário.

---

## Literatura de apoio (fora do corpus, citável na fundamentação)

Não contam como estudos primários. Falham em CI2 ou CI5, mas fornecem conteúdo para seções específicas.

| Referência | Veículo | Ano | Status | Uso previsto | Ressalva |
|---|---|---|---|---|---|
| MOISEI, L.; MOCANU, L. *Mitigating OWASP API security top 5 risks through API gateway patterns* | TRUST (TU Moldova) | 2025 | Lido (íntegra) | Coluna de mitigação da matriz para API1 a API5. Parágrafos de limitação citáveis (gateway "not sufficient alone") | Genérico de API, não menciona FHIR ou saúde. Sem metodologia. 9 referências, maioria literatura cinzenta. A adaptação ao contexto FHIR fica a cargo deste trabalho |
| NOWROZY, R. et al. *Privacy Preservation of EHR in the Modern Era: A Systematic Survey* | ACM Computing Surveys | 2024 | Lido (íntegra) | (1) Referência metodológica: aplicação documentada de Kitchenham em informática em saúde, venue de primeira linha. 5 SQs, 5 bases, snowballing, funil 513 → 162 → 130. (2) Citação p. 30: "none of the existing studies tested their proposed method using either real samples or raw data of EHRs". Justifica a ênfase do TCC em Opie e Brüggemann como evidência empírica | Zero menções a FHIR, OWASP, SMART, OAuth ou API em 37 páginas. É survey de privacidade de EHR (acesso, blockchain, nuvem, criptografia). Falha CI2 e CI3. Não é fonte sobre o objeto do TCC |
| LÓPEZ MARTÍNEZ, A. et al. *A Comprehensive Model for Securing Sensitive Patient Data in a Clinical Scenario* | IEEE Access | 2023 | Lido (íntegra) | Uso opcional. Tabela 2 lista FHIR entre 11 protocolos clínicos com "MitM, replay, escalabilidade horizontal" como fraquezas. Seção II-B cobre HIPAA, GDPR, EHDS, Data Governance Act. Aplica NIST SP 800-63 (IAL/AAL/FAL) a níveis de autenticação | Domínio é laboratório clínico (LIS, analisadores, middleware), não API FHIR. FHIR ocupa uma linha de tabela. Threat model (Tabela 4) é genérico: malware, phishing, DoS. 22 requisitos, maioria com "Blockchain-based" como mecanismo. Única validação empírica é visita a hospital espanhol para o fluxo, não para vulnerabilidades. Anotação prévia em busca_artigos: "secundário, talvez terciário" |
| MARCUS, A. *Security on FHIR* | Asymmetrik (slides) | 2018 | Não lido | Contexto histórico, se necessário | Slides, fora de CI1. Uso mínimo |
| CHATTERJEE, A. et al. *SFTSDH: Applying Spring Security Framework With TSD-Based OAuth2 to Protect Microservice Architecture APIs* | IEEE Access | 2022 | Lido (seção de testes) | Seção B testa XSS, clickjacking, content sniffing, CSRF e força bruta em API de saúde (eCoach), com os mesmos headers que Opie encontrou ausentes (X-Frame-Options, X-Content-Type-Options, X-XSS-Protection). Contraste para a matriz: mitigação existe (Chatterjee), deploy padrão não usa (Opie). OAuth2 + RBAC + lockout após 3 falhas. OWASP como taxonomia, GDPR 11 menções. **Candidato a primário se CI2 for relaxado** | FHIR explicitamente fora do escopo ("beyond the scope of this paper"). **Erro no texto:** prosa troca os headers (diz que nosniff bloqueia clickjacking e DENY bloqueia sniffing; é o inverso). Citar a Tabela 7, não o parágrafo |
| BALAGANSKI, A. *API Security Management* | KuppingerCole (relatório de analistas) | 2025 | PDF em `bibliografia/balaganski-api-security-market-analysis-2025.pdf` | Opcional. Contexto de mercado para a introdução: como a indústria enxerga segurança de API. Menciona HIPAA | Literatura cinzenta comercial, sem revisão por pares. Não é sobre FHIR. Corrigido de 2015 para 2025 conforme busca_artigos; passa CI1 mas não é estudo primário |
| AL-RUMAIM, A.; PAWAR, J. D. *Exploring the Evolving Landscape of API Security Challenges in the Healthcare Industry* | IEEE SIN | 2023 | Lido (íntegra) | **Descartar.** Se precisar de "lacuna de estudos de caso", usar Brüggemann seção 10 ou Nowrozy | Abstract promete "quantitative findings" que não existem. 8 de 21 referências são sobre malware Android sem relação. Tabela I contradiz o texto. Erros de digitação |

---

## Camada 3: Excluídos

### Por CE6 (veículo sem revisão por pares verificável)

| Referência | Veículo | Ano |
|---|---|---|
| SURISETTY, L. S. *Zero-trust data fabrics* | ijarcst.org | 2021 |
| SURISETTY, L. S. *Proactive threat mitigation in API ecosystems* | ijarcst.org | 2023 |
| AKIB RAHMAN, S. S. *A HIPAA-compliant web application design framework* | ijrt.org | 2024 |
| GUDI, S. R. *AI-Driven Cloud-Native Microservices Framework* | internationaljournalssrp.org | 2026 |
| HALE, C. J. P. *Design and Implementation of a Secure Cloud and Network Framework* | ijctece.com | 2025 |
| VASA, R. *Zero-Trust Security in Cloud API Integrations* | tpmap.org | 2025 |
| BALAMURUGAN, R. *Enterprise-Grade Secure API Management using Deep Learning* | ijeetr.com | 2022 |
| SUGUMAR, R. *Cyber-Secure Cloud Architecture... SAP Healthcare* | ijhit.info | 2025 |
| RAMAKRISHNA, S. *Cybersecurity Aware Cloud Native AI Framework* | ijrpetm.com | 2024 |
| HELLSTRÖM, O. W. *A Secure-by-Design Cloud-Native Framework* | ijeetr.com | 2024 |
| HUBER, T. A. *Secure Software Testing and Validation Frameworks for SAP* | ijrpetm.com | 2026 |
| SRIRAMOJU, S. *Architectural Frameworks for Secure Inter-Enterprise Integration* | iadier-academy.org | 2026 |
| DAMARCHED, M. K. *A HIPAA-Aware Agentic AI Co-Pilot Framework* | J. Drug Delivery & Therapeutics | 2026 |
| RAHAMAN, M. D. A. *Connected but Compliant* | sem veículo identificado | s.d. |
| ŠAFRAN, V. et al. *A Scalable and Secured HL7 FHIR Healthcare Platform* | npublications.com | 2025 |

**Nota (18 set):** Surisetty 2021, Surisetty 2023 e Akib Rahman foram anotados como "relevante" ou "tangencial" em busca_artigos após leitura de abstract. A relevância temática não anula o CE6. Se o critério for mantido, os três ficam fora. PDFs de Surisetty 2021 e Rahaman estão em `artigos/` e podem ser removidos.

### Por CI6 (idioma)

| Referência | Motivo |
|---|---|
| PARK, Y. M. *Enhancing Security and Interoperability of Medical Information Exchange Using FHIR and Blockchain* (JDCS, 2025) | Corpo em coreano. Traduzido do coreano por Claude em 18 de setembro: veículo legítimo (KCI), mas contribuição central é blockchain, baseline "FHIR only" sem métrica definida, HIPAA explicitamente fora do escopo. Exclusão por CI6 não perde conteúdo |

### Por CE1 / CE2 (fora do escopo)

| Referência | Motivo |
|---|---|
| SANTOS, L. S. et al. *Interoperability and Security... SMART on FHIR* (J. Health Informatics, 2023) | **Movido da Camada 1 em 18 set.** Abstract lido: foco em interoperabilidade e autenticação em IoT, não em SMART. CE1 |
| KIM HOANG LE, T. et al. *Enhancing Healthcare Interoperability with FHIR* (ACM, 2024) | CE1: interoperabilidade, sem segurança |
| SHOUMIK, F. S. et al. *Scalable micro-service based approach to FHIR server* (IEEE, 2017) | CE1 + fora de CI1 |
| FERREIRA, J. C. et al. *Multi-component pipeline LLMs* (2026) | CE1: LLM/interoperabilidade |
| GOMES, F. M. M. *Utilização da Tecnologia SDR... IoMT* (2023) | CE2: camada física/RF |
| MONTENEGRO MARTÍNEZ, G. A. et al. *Auditoría... medidor de glucosa* (2025) | CE2: dispositivo IoT |
| ABIRAMI, S. K. et al. *zk-ID* | CE1: identidade/blockchain |
| YANG, C.-N. et al. *Ensuring FHIR Authentication and Data Integrity by Smart Contract and Blockchain* (ACM, 2023) | CE1. Abstract lido em 17 set: "we use (F)HIR and (E)thereum smart contract to ensure data Integrity in (E)MR (FEE)". Blockchain para integridade, não segurança de API |
| ADOHINZIN, O.; HARRATH, Y. *A Systematic Review of IDS for Internet of Medical Things* (ACM, 2026) | CE2. Abstract lido em 18 set: IDS para IoMT, fora do recorte |
| KHANNA, R.; NANDAL, J. *CAPF: Clinical Agent Permission Framework* (2026) | CE1: agentes de IA |
| CHANDER, B. *Zero-Trust Continuous Authentication Using Biometric Pulse* (ACM DTRAP, 2026) | CE2. Lido em 15 set: protocolo criptográfico para IoT médico (HMAC, biometria de pulso, ECC), camada de dispositivo e transporte. FHIR só em trabalhos futuros, zero API/OAuth/OWASP. Venue legítimo, paper consistente, fora do escopo |
| XIONG, Y. et al. *Distributed Architecture for Genomic Data* (ACM, 2025) | CE1: dados genômicos |
| BA, Y. et al. *Non-Interactive Multi-Client Searchable Encryption* (IEEE, 2026) | CE1: criptografia aplicada |
| PRAYITNO, E. et al. *Hybrid Post-Quantum Cryptography for EHR* (2025) | CE1: criptografia pós-quântica |
| HAYES, A. M. *DICOMweb and HL7 FHIR medical image sharing* (2026) | CE1 + preprint |
| JAYATHISSA, P.; HEWAPATHIRANA, R. *HAPI-FHIR Server Implementation... Sri Lanka* (arXiv, 2024) | CE1 + CE3. Lido em 16 set: 116 menções a FHIR, zero a OAuth, OWASP, HIPAA ou vulnerabilidade. Todas as menções a segurança são genéricas, sem mecanismo nomeado. Relato de implantação focado em interoperabilidade. Preprint |
| KÖRBER, Y. et al. *A SMART on FHIR conformant infrastructure for patient reported outcomes* (Sage, 2025) | CE1. Abstract: SMART on FHIR como ferramenta para o projeto H2O, não como objeto de análise de segurança |
| KÜFNER, J. J. K. et al. *Enhancing Medical Device Security: Exploiting GUI Vulnerabilities* (Springer, 2023) | CE2. GUI de dispositivo médico, não API |

### Por CE3 / fora de CI1

| Referência | Motivo |
|---|---|
| KATARU, A. *AgentCare-Guard: A Capability-Constrained Reference Monitor for Agentic FHIR Transactions* | CE3: preprint, "not been peer reviewed by a journal". Também CE1 (agentes de IA) |
| DEMURJIAN, S. A. et al. *Alternative Approaches for Supporting LBAC in FHIR* (WEBIST, 2020) | Fora de CI1. Tema é pertinente (controle de acesso em FHIR). Exceção documentada pode ser aberta se o orientador julgar adequado; o default é exclusão |
| SAHA, S. et al. *A cloud security framework for a data centric WSN application* (ACM, 2016) | Fora de CI1 + CE2 |
| AL-HAMDANI, W. A. *XML security in healthcare web systems* (ACM, 2010) | Fora de CI1 |
| ARANHA, H. et al. *Securing Mobile e-Health Environments by Design* (IEEE WiMob, 2019) | Fora de CI1. Abstract lido em 10 set, anotado como irrelevante. Sem motivo para exceção |
| WAN, Y. *Computing-Empowered E-Health and Telemedicine* (HIDA 2026, ACM ICPS) | **Lido na íntegra em 18 set. Não citar.** Apresenta resultados de outros papers como próprios (FedSepsis é ref. [2], CloudDL é ref. [3]). Citações fantasmas ([4] e [5] são editoriais, não os trabalhos descritos). Números mudam entre seções (63,8% vs 63,2%; 47% vs 78,3%; seis vs três hospitais). Figura 2 com texto corrompido típico de geração por IA. Título com placeholder de template. Autor único, afiliação em empresa de tecnologia esportiva, email QQ. 10 referências. FHIR só decorativo. Serve como exemplo do que o CE3/CE6 devem barrar |
| PARKER, M. et al. *Managing third-party risk* (livro, 2023) | CE3: capítulo de livro |
| ASIA CCS '26 / CCS '25 (proceedings) | CE3: anais completos |

---

## Placar (18 set)

| Categoria | Qtd |
|---|---|
| Primários confirmados (lidos) | 4 |
| Candidatos a primário | 0 |
| Talvez | 0 |
| Literatura de apoio | 7 (6 utilizáveis, Al-Rumaim descartado) |
| Excluídos | 42 |
| **Total** | **53** |

**Triagem das strings A a D concluída em 18 set.** Corpus fecha em **4 primários** com CI2 estrito, **5** se CI2 for relaxado para incluir Chatterjee. Três caminhos para ampliar, não excludentes: (a) string E focada em SMART em IEEE/ACM; (b) relaxar CI2 para segurança de API em saúde sem exigir FHIR explícito; (c) tratar a escassez como achado do mapeamento, com o corpus complementado por literatura de apoio. Os quatro primários cobrem quatro métodos e quatro funções distintas (ver tabela de triangulação), o que sustenta o corpus pela qualidade.

---

## Registro de decisões

| Data | Decisão | Motivo |
|---|---|---|
| 13 set | Adicionado CE6 | 18 de 51 candidatos em veículos predatórios |
| 18 set | Santos 2023: Camada 1 → Camada 3 | Abstract: IoT, não SMART (CE1) |
| 18 set | Park 2025: Camada 2 → Camada 3 | CI6. Lido na íntegra, conteúdo não justifica exceção |
| 18 set | Moisei & Mocanu: Camada 1 → Apoio | Genérico de API, sem FHIR, sem validação (CI2, CI3, CI5) |
| 18 set | Al-Rumaim: Camada 2 → Apoio, descartar | Qualidade insuficiente. Substituído por Brüggemann seç. 10 e Nowrozy para "lacuna" |
| 18 set | Brüggemann: confirmado primário | Lido na íntegra. Segunda fonte empírica, resolve dependência de Opie |
| 18 set | Tharaka: confirmado primário com ressalva | Lido na íntegra. Documentation-based, não testa. Vale por presença/ausência de controle |
| 18 set | Adicionada coluna "Método" e tabela de triangulação | Primários com métodos diferentes sustentam a triangulação |
| 18 set | AgentCare-Guard: excluído | Preprint (CE3) |
| 18 set | Nowrozy: Camada 1b → Apoio | Lido na íntegra. ACM CSUR, Kitchenham bem aplicado, mas zero FHIR/API. Vale como referência de método e pela citação sobre falta de validação empírica |
| 18 set | López Martínez: Camada 1b → Apoio (opcional) | Lido na íntegra. Laboratório clínico, não API. FHIR é uma linha de tabela. Threat model genérico |
| 18 set | Šafran: Camada 1b → Camada 3 | CE6 (npublications.com) |
| 18 set | Demurjian: Camada 1b → Camada 3 | CI1 (2020). Exceção pendente de decisão do orientador |
| 18 set | Yang, Aranha, Adohinzin: Camada 2 → Camada 3 | Abstracts lidos e anotados como irrelevantes em busca_artigos (blockchain, fora de CI1, IDS para IoMT) |
| 18 set | Balaganski: Camada 3 → Apoio (opcional) | Versão de 2025 localizada e baixada. Passa CI1. Relatório de analistas, não estudo primário |
| 18 set | Wan e Chander: lidos na íntegra, exclusão confirmada | Wan: apropriação de resultados e sinais de geração por IA. Chander: legítimo mas camada de dispositivo |
| 18 set | Chatterjee: Camada 2 → Apoio (candidato a primário se CI2 relaxar) | Seção de testes cobre XSS, clickjacking, headers. FHIR fora do escopo declarado. Erro de troca de headers na prosa |
| 18 set | Jayathissa, Körber, Küfner: Camada 2 → Camada 3 | CE1, CE1, CE2. Jayathissa lido: segurança só genérica |
| 18 set | Ranatunge: Camada 2 → Camada 1 (4º primário) | Lido na íntegra. Única implementação SMART server-side do corpus, granularidade por recurso em produção, explica causas da lacuna de adoção. Anotação "tangencial" pelo abstract foi revertida |

---

## Protocolo de leitura para a Camada 2

Para cada item, ler nesta ordem e parar assim que houver decisão:

1. **Título**: o recorte é FHIR/API/segurança em saúde? Se não, excluir.
2. **Abstract**: há contribuição de segurança (não só interoperabilidade)? Há validação (experimento, medição, pentest, auditoria)? Se não houver nenhuma das duas, excluir por CE1 ou CE5.
3. **Conclusão**: os achados são utilizáveis para a matriz OWASP × SMART × HIPAA? Se sim, incluir e registrar quais categorias OWASP o trabalho toca.

Registrar a decisão no "Registro de decisões" acima, com data.

---

## Pendências

### Decisões do orientador
1. **Tamanho do corpus.** Quatro primários bastam para o TCC I, ou rodar string E (`"SMART on FHIR" AND (authorization OR "access control" OR scope)`) em IEEE/ACM?
2. **CI2.** Manter foco estrito em FHIR/SMART, ou relaxar para segurança de API em saúde? A segunda opção traz Chatterjee como primário.
3. **Demurjian (2020).** Abrir exceção ao CI1 por pertinência do tema?

### Tarefas
4. **Chatterjee**: se CI2 relaxar, ler o restante (só a seção de testes foi lida) e promover a primário.
5. **Cap. 4 de Opie**: única parte não lida. Necessário se o TCC II for replicar testes.
6. **Follow-up Opie**: por volta de 25 set. Depois disso, assumir que não vem.
7. **PDFs em `artigos/`**: Surisetty 2021 e Rahaman são CE6. Remover ou manter fora do corpus.
