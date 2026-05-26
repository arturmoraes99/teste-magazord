# Teste para vaga de Estagio em Analista de Qualidade Magazord - Artur Moraes Espindola da Silva

---

## Teste — Casos de Teste: Validacao de Dados Cadastrais

O sistema possui os seguintes campos de cadastro avaliados: Nome completo, E-mail, Numero de telefone, Data de nascimento e Endereco (rua, cidade, estado e CEP).

---

### Nome Completo

| ID | Descricao | Entrada | Resultado Esperado |
|----|-----------|---------|-------------------|
| CT01 | Nome valido com duas palavras | "Maria Silva" | Aceito |
| CT02 | Nome valido com tres palavras | "Maria Silva Santos" | Aceito |
| CT03 | Nome com acentos e apostrofo | "Joao D'Avila" | Aceito |
| CT04 | Nome com apenas um termo | "Maria" | Rejeitado — nome incompleto |
| CT05 | Nome com numeros | "Maria 123" | Rejeitado — caracteres invalidos |
| CT06 | Nome com caracteres especiais | "Maria @Silva!" | Rejeitado |
| CT07 | Campo vazio | "" | Rejeitado — campo obrigatorio |
| CT08 | Nome com espacos em branco apenas | "   " | Rejeitado |
| CT09 | Nome muito longo | 300 caracteres | Rejeitado |


---

### E-mail

| ID | Descricao | Entrada | Resultado Esperado |
|----|-----------|---------|-------------------|
| CT10 | E-mail valido | "usuario@email.com" | Aceito |
| CT11 | E-mail com subdominio | "user@mail.empresa.com.br" | Aceito |
| CT12 | Sem @ | "usuarioemail.com" | Rejeitado — formato invalido |
| CT13 | Sem dominio apos @ | "usuario@" | Rejeitado |
| CT14 | Sem texto antes do @ | "@email.com" | Rejeitado |
| CT15 | Com espacos | "usuario @email.com" | Rejeitado |
| CT16 | Campo vazio | "" | Rejeitado — campo obrigatorio |
| CT17 | E-mail com caracteres especiais invalidos | "usuario#!@email.com" | Rejeitado |
---

### Telefone

| ID | Descricao | Entrada | Resultado Esperado |
|----|-----------|---------|-------------------|
| CT18 | Celular valido com mascara | "(47) 99999-9999" | Aceito |
| CT19 | Fixo valido com mascara | "(47) 3333-3333" | Aceito |
| CT20 | Numero sem mascara | "47999999999" | Aceito — sistema deve formatar |
| CT21 | Com letras | "(47) 9ABCD-1234" | Rejeitado |
| CT22 | Incompleto | "(47) 999" | Rejeitado — digitos insuficientes |
| CT23 | Com caracteres especiais | "(47) 9####-9999" | Rejeitado |
| CT24 | Campo vazio | "" | Verificar se e obrigatorio |
| CT25 | DDD invalido | "(00) 99999-9999" | Rejeitado — DDD inexistente |
| CT26 | Numero internacional | "+1 99999-9999" | Verificar se o sistema suporta |

---

### Data de Nascimento

| ID | Descricao | Entrada | Resultado Esperado |
|----|-----------|---------|-------------------|
| CT27 | Data valida de adulto | "15/06/1995" | Aceito |
| CT28 | Data futura | "01/01/2090" | Rejeitado — data invalida |
| CT29 | Data de hoje | Data atual | Rejeitado — idade invalida |
| CT30 | Dia invalido | "31/02/2000" | Rejeitado — fevereiro nao tem 31 dias |
| CT31 | Mes invalido | "15/13/2000" | Rejeitado — mes inexistente |
| CT32 | Menor de idade | Data que resulte em menos de 18 anos | Verificar regra de negocio |
| CT33 | Formato incorreto | "1995-06-15" | Rejeitado |
| CT34 | Campo vazio | "" | Rejeitado — campo obrigatorio |
| CT35 | Ano bissexto valido | "29/02/2000" | Aceito |
| CT36 | Ano bissexto invalido | "29/02/2001" | Rejeitado — 2001 nao e bissexto |

---

### Endereco / CEP

| ID | Descricao | Entrada | Resultado Esperado |
|----|-----------|---------|-------------------|
| CT37 | CEP valido com mascara | "89280-831" | Aceito |
| CT38 | CEP valido sem mascara | "89280831" | Aceito — sistema deve formatar |
| CT39 | CEP inexistente | "00000-000" | Rejeitado ou aviso de CEP nao encontrado |
| CT40 | CEP com letras | "8928A-831" | Rejeitado |
| CT41 | CEP incompleto | "8928" | Rejeitado — menos de 8 digitos |
| CT42 | CEP com mais de 8 digitos | "892808319" | Rejeitado |
| CT43 | Campo CEP vazio | "" | Rejeitado — campo obrigatorio |
| CT44 | Estado invalido (sigla inexistente) | "XX" | Rejeitado |
| CT45 | Cidade deixada em branco manualmente | "" | Rejeitado — campo obrigatorio |
| CT46 | Rua com caracteres especiais | "Rua Joao D'Avila, 123" | Aceito |
| CT47 | Endereco muito longo | 500 caracteres no campo rua | Rejeitado |
---

## Desafio — Analise do Erro na Importacao do XML GNRE

---

### Erro Retornado pelo Sistema

| Campo | Valor |
|-------|-------|
| Codigo do Erro | 700 |
| Campo com problema | `item_referencia` |
| Descricao | Referencia deve ser a mesma do vencimento informado |

---

### Identificacao do Problema no XML

Analisando o XML exportado, os dois campos em conflito sao:

**Campo `<referencia>`** — indica o periodo de referencia da guia:

```xml
<referencia>
    <mes>05</mes>   <!-- Maio -->
    <ano>2023</ano> <!-- 2023 -->
</referencia>
```

**Campo `<dataVencimento>`** — indica a data de vencimento da guia:

```xml
<dataVencimento>2024-01-11</dataVencimento> <!-- Janeiro de 2024 -->
```

A GNRE exige que o mes e ano da referencia sejam os mesmos da data de vencimento. No XML enviado, a referencia aponta para maio/2023, enquanto o vencimento esta em janeiro/2024 — uma divergencia de 8 meses, o que viola a regra de validacao do portal e gera o erro de codigo 700.

---

### Correcao Necessaria

O campo `<referencia>` deve ser ajustado para refletir o mesmo periodo da data de vencimento.

```xml
<referencia>
    <mes>01</mes>
    <ano>2024</ano>
</referencia>
<dataVencimento>2024-01-11</dataVencimento>
```

---
