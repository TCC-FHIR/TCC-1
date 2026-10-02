# Matriz de cobertura: OWASP API Top 10 x FHIR x SMART on FHIR x HIPAA

**Versão 2, rascunho. 2 de outubro de 2026.**

Substitui a versão de 4 de setembro (`matriz_owasp_fhir_hipaa_completa_pt.png`), que cobria apenas os achados de Opie e misturava a especificação FHIR com o SMART on FHIR numa coluna só.

Duas mudanças de fundo nesta versão:

1. **Separação entre a especificação FHIR e o SMART on FHIR.** São camadas distintas. O FHIR define recursos e a API REST; o SMART define autenticação e autorização por cima. Uma lacuna pode existir em uma e não na outra.
2. **Coluna de tipo de lacuna.** Lacuna de design significa que a especificação não trata do assunto. Lacuna de adoção significa que a especificação trata, mas as implementações em campo não aplicam. As duas são ameaças práticas pelos critérios adotados, mas pedem respostas diferentes: a primeira exige mudança de norma, a segunda exige configuração.

---

## Matriz

| # | Vulnerabilidade | OWASP 2023 | Spec FHIR | SMART | HIPAA | Lacuna | Fonte |
|---|---|---|---|---|---|---|---|
| 1 | Ausência de rate limiting | API4 | não trata | não trata | §164.306(a)(1) disponibilidade | design | Opie |
| 2 | XSS refletido | API3, API8 | não trata | não trata | §164.312(c)(1) integridade | design | Opie |
| 3 | Clickjacking | API8 | não trata | não trata | §164.312(c)(1) | design | Opie |
| 4 | PRSSI | API8 | não trata | não trata | §164.312(c)(1) | design | Opie |
| 5 | Vazamento de referrer | API8 | não trata | não trata | §164.502(b) minimum necessary | design | Opie |
| 6 | Granularidade de escopo | API1, API3, API5 | parcial | parcial | §164.502(b) | design parcial | Opie, Ranatunge |
| 7 | Endpoint sem autenticação | API2 | delega ao SMART | cobre | §164.312(d) autenticação | adoção | Brüggemann |
| 8 | Ausência de TLS | API8 | exige | exige | §164.312(e)(1) transmissão | adoção | Brüggemann, Tharaka |
| 9 | Versão com CVE conhecida (XXE) | API9, API8 | não trata XML | não trata | §164.308(a)(1) análise de risco | adoção | Brüggemann |
| 10 | Payload sem criptografia nem assinatura | sem categoria | não trata | não trata | §164.312(e)(1) | design | Tharaka |

---

## Evidência por linha

**1. Rate limiting.** Opie saturou CPU e memória do IBM FHIR Server com requisições concomitantes, mesmo com SMART configurado via Keycloak. Nem a especificação FHIR nem o SMART mencionam limite de requisições.

**2 a 5. Vulnerabilidades de cliente.** Opie encontrou XSS refletido, clickjacking por ausência de `X-Frame-Options`, importação de folha de estilo por caminho relativo e vazamento de identificadores de paciente em cabeçalhos `Referer`. Todas em servidor com autenticação correta, o que mostra que o SMART não alcança essa camada.

**6. Granularidade.** Opie mostrou que escopos SMART amplos devolvem mais dados que o necessário. Ranatunge implementou controle combinando recurso e papel (um laboratório altera `DiagnosticReport` mas não `Patient`), e relata que a revisão de literatura deles não achou implementação equivalente do lado servidor. A classificação "design parcial" reflete isso: o SMART define escopos, mas a granularidade que o HIPAA exige fica a cargo de quem implementa.

**7. Autenticação ausente.** Brüggemann achou 1.477 endpoints FHIR públicos; 242 devolvem a lista de pacientes sem autenticação, somando 2,1 milhões de registros. A Tabela 3 do artigo mostra que endpoints anunciando SMART ou OAuth quase não vazam, enquanto 58% não anunciam método algum. O controle existe e não é usado.

**8. TLS.** Dois terços dos endpoints FHIR de Brüggemann não usam TLS. Tharaka confirma pelo outro lado: os cinco EMRs suportam TLS, mas a configuração fica com quem implanta.

**9. Versão desatualizada.** Brüggemann encontrou 40 sistemas FHIR afetados pela CVE-2024-51132, CVSS 9.8, corrigida no HAPI FHIR 6.4.0. O artigo não declara a classe da vulnerabilidade; a identificação como XXE vem do NVD e precisa ser citada à parte. A página de segurança do FHIR R4 não menciona parser XML, XXE, DTD nem expansão de entidade, o que acrescenta um componente de design a uma falha que no essencial é de manutenção.

**10. Payload.** Tharaka mostra que nenhum dos cinco EMRs cifra ou assina mensagens FHIR; tudo depende de TLS. O risco aparece quando um intermediário termina a conexão. Não há categoria OWASP para isso.

---

## Dois achados sobre o próprio OWASP

As linhas 9 e 10 não encontram categoria direta no OWASP API Security Top 10 de 2023.

Para XXE, verificação no texto oficial: nenhuma ocorrência de "XML" nas 849 linhas, e "injection" aparece uma vez, de passagem, no impacto do API10. A categoria Injection existia como API8:2019 e foi retirada. O encaixe em API8 se apoia em dois pontos do texto: a lista "Is the API Vulnerable?" inclui patches ausentes e sistemas desatualizados, e o cenário de exemplo do API8 é o Log4Shell, estruturalmente igual a um XXE. O Top 10 Web seguiu o mesmo caminho: XXE era A4:2017 e foi absorvido por A05:2021.

Para ausência de criptografia em nível de mensagem, não há encaixe. API8 cobre configuração, e aqui o recurso não existe em nenhuma das implementações.

Esse par sustenta a tese central: o OWASP API Top 10 sozinho não é suficiente para avaliar segurança em FHIR.

---

## Mitigações

| # | Mitigação | Onde | Fonte |
|---|---|---|---|
| 1 | Limite por cliente ou token | gateway reverso | Moisei (API4) |
| 2 a 4 | `Content-Security-Policy`, `X-Frame-Options`, sanitização de entrada | gateway ou aplicação | Opie cap. 6, Chatterjee (testado) |
| 5 | `Referrer-Policy: no-referrer` | gateway | Opie cap. 6 |
| 6 | Controle por recurso combinado com papel | servidor de autorização | Ranatunge (implementado) |
| 7 | Implantar servidor de autorização SMART | infraestrutura | Ranatunge |
| 8 | TLS 1.2 ou superior, certificado válido | infraestrutura | Tharaka |
| 9 | Atualizar HAPI para 6.4.0 ou superior; inventário de versões | operação | Brüggemann |
| 10 | sem mitigação padronizada | | |

Moisei registra o limite dessa abordagem: o gateway filtra violações evidentes, mas não tem contexto de negócio para decidir autorização em nível de objeto. As linhas 1 a 5 se resolvem na borda; a 6 não.

---

## Pendências

- Confirmar se o suporte a XML é obrigatório para servidores FHIR (`hl7.org/fhir/R4/http.html` e CapabilityStatement). Se for, a linha 9 ganha peso como lacuna de design.
- Confirmar a data de lançamento do HAPI 6.4.0 para quantificar o atraso dos servidores escaneados em outubro de 2025.
- Confirmar a classe da CVE-2024-51132 no NVD.
- Decidir se Ranatunge permanece como estudo primário. A anotação de 25 de setembro observa que o artigo descreve apenas a implementação do Sri Lanka, sem discutir FHIR em geral.
- Revisar as citações de HIPAA linha a linha. As desta versão foram conferidas contra `hipaa-simplification-201303.pdf`, mas a atribuição de cada vulnerabilidade a um dispositivo é interpretação, não está nas fontes.
