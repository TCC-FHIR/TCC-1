# Triagem de artigos: resumo

Resumo da seleção de estudos para o mapeamento sistemático, feita entre 9 e 18 de setembro de 2026 a partir de quatro strings de busca em Google Scholar, IEEE Xplore, ACM Digital Library e Springer Link. A versão completa, com o motivo de cada decisão, está em `triagem_artigos.md`.

## Pergunta de pesquisa

Versão de trabalho, ainda a ser fechada com o orientador.

**Quais lacunas de segurança existem entre os controles definidos pelo SMART on FHIR e as categorias do OWASP API Security Top 10 (2023), e quais delas constituem ameaças práticas em implementações reais de servidores FHIR, considerando os requisitos do HIPAA?**

A leitura dos estudos primários levou a distinguir dois tipos de lacuna, e a pergunta deve refletir isso:

- **Lacuna de design**: a especificação SMART on FHIR não cobre a categoria. Exemplo: rate limiting (API4), headers de proteção client-side (API8).
- **Lacuna de adoção**: a especificação cobre, mas as implementações em campo não a usam. Exemplo: 58% dos servidores FHIR expostos na internet não anunciam nenhum método de autenticação (API2).

Perguntas secundárias:

1. Quais categorias do OWASP API Top 10 se aplicam a APIs FHIR?
2. Quais controles o SMART on FHIR define, e qual categoria cada um cobre?
3. Onde a cobertura falha por design, e onde falha por adoção?
4. Como cada lacuna se relaciona com os requisitos do HIPAA, em especial o minimum necessary (§ 164.502(b))?
5. Que mitigações a literatura propõe para as lacunas identificadas?

## Critérios de seleção

**Inclusão** (todos devem valer):

| | |
|---|---|
| CI1 | Publicado entre 2021 e 2026 |
| CI2 | Trata de segurança em APIs FHIR ou SMART on FHIR |
| CI3 | Segurança de API no domínio de saúde |
| CI4 | Menciona HIPAA ou requisitos regulatórios de segurança em saúde |
| CI5 | Descreve vulnerabilidades em implementação real, não só em teoria |
| CI6 | Inglês ou português |

**Exclusão** (qualquer um basta):

| | |
|---|---|
| CE1 | FHIR só como interoperabilidade, sem segurança |
| CE2 | Segurança de rede física ou dispositivo, sem foco em API |
| CE3 | Resumo, pôster, preprint sem revisão por pares, ou sem método descrito |
| CE4 | Duplicata |
| CE5 | Puramente teórico, sem validação |
| CE6 | Veículo sem revisão por pares verificável ou com sinais de periódico predatório |

O CE6 foi acrescentado durante a triagem. Dos 53 candidatos, 18 vinham de periódicos com domínio genérico, sem DOI de editora reconhecida e sem corpo editorial rastreável. O critério está documentado no protocolo.

Dois estudos que não passam em todos os critérios de inclusão ainda podem ser citados na fundamentação teórica como literatura de apoio. Eles não contam no corpus do mapeamento.

## Números

| Etapa | Quantidade |
|---|---|
| Candidatos únicos das quatro strings | 53 |
| Excluídos por critério | 42 |
| Literatura de apoio (fora do corpus) | 7 |
| **Estudos primários** | **4** |

Dos 42 excluídos: 15 por CE6, 19 por escopo (CE1 ou CE2), 7 por CE3 ou por data, 1 por idioma.

## Camada 1: estudos primários

Os quatro foram lidos na íntegra. Cada um usa um método diferente e responde a uma parte diferente da pergunta.

**Opie (2024)**, dissertação de mestrado, Universidade do Havaí.
Teste de penetração em um IBM FHIR Server com SMART on FHIR configurado via Keycloak. Encontrou sete vulnerabilidades mesmo com o SMART correto: rate limiting ausente, XSS, clickjacking, vazamento de referrer. Mostra o que a especificação não cobre. Discute o "regulatory blind spot" entre HIPAA e o 21st Century Cures Act.

**Brüggemann et al. (2026)**, IEEE European Symposium on Security and Privacy.
Scan de todo o IPv4 e parte do IPv6 atrás de servidores FHIR, HL7 e DICOM expostos. Achou 1.477 endpoints FHIR; 242 entregam a lista de pacientes sem autenticação; dois terços sem TLS; 40 com CVE de severidade 9.8. Endpoints que anunciam SMART ou OAuth quase não vazam. Mostra o tamanho da lacuna de adoção. Código aberto.

**Tharaka et al. (2026)**, ACM AMASS.
Comparação de cinco EMRs open source (OpenEMR, OpenMRS, GNU Health, Oscar, Bahmni) em autenticação, criptografia, interoperabilidade, RBAC e conformidade. Baseado em documentação, sem testes. Nenhum dos cinco cifra ou assina payload FHIR; um usa Basic auth na API. Conclui que conformidade depende de configuração, não vem por padrão.

**Ranatunge e Karunapema (2025)**, MEDINFO.
Implementação em produção, no Ministério da Saúde do Sri Lanka, de um servidor de autorização SMART on FHIR para o fluxo backend services. Resolve controle de acesso por recurso (um laboratório edita DiagnosticReport, mas não Patient). É a única implementação server-side completa encontrada. Os autores explicam por que a adoção é rara: o guia de implementação é complexo e falta capacidade técnica.

Com o CI2 como está, o corpus fecha em quatro. Se o critério for relaxado para aceitar segurança de API em saúde sem FHIR explícito, entra um quinto: Chatterjee et al. (2022, IEEE Access), que testa os mesmos headers client-side que Opie encontrou ausentes, mas num sistema de coaching digital, não em FHIR.

## Camada 2: candidatos

Esvaziada em 16 de setembro. Todos os candidatos foram lidos e classificados.

## Literatura de apoio

Sete trabalhos ficaram fora do corpus mas serão citados: Moisei e Mocanu (mitigações por gateway para API1 a API5), Nowrozy et al. (survey em ACM Computing Surveys, referência de método), Chatterjee et al. (testes de headers), López Martínez et al. (cobertura regulatória), Balaganski (relatório de mercado), Marcus (slides de 2018) e Al-Rumaim e Pawar (a descartar por qualidade).

## Protocolo de leitura

Cada candidato foi lido nesta ordem, parando assim que houvesse decisão:

1. **Título.** O recorte é FHIR, API ou segurança em saúde? Se não, excluir.
2. **Abstract.** Há contribuição de segurança, e não só de interoperabilidade? Há validação (experimento, medição, teste, auditoria)? Sem nenhuma das duas, excluir.
3. **Conclusão.** Os achados servem para a matriz OWASP × SMART × HIPAA? Se sim, incluir e anotar quais categorias OWASP o trabalho toca.

Dois casos mostraram o limite do abstract: Ranatunge parecia tangencial e virou primário no texto completo; Chatterjee parecia genérico e tem a única bateria de testes de headers além de Opie. Por isso nenhum candidato com título aderente foi excluído sem leitura do texto.

Cada decisão está registrada com data em `triagem_artigos.md`.
