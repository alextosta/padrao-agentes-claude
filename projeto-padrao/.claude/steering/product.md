# Steering: Produto

> **Template.** Preencha por projeto e apague estas instruções. Este documento é lido pelo agente em
> toda sessão: ele responde "para quem estamos construindo e o que **não** vamos construir".
> Mudança aqui é decisão consciente — não é changelog de feature.

---

## Em uma frase

{{O que o sistema faz, para quem, e qual decisão ou processo ele viabiliza.}}

## Problema

{{Qual é a dor hoje: como o processo é feito sem o sistema, quanto custa (tempo, retrabalho, risco),
e o que acontece se nada for feito. Escreva em termos do órgão, não em termos de tecnologia.}}

## Público

**Primário**: {{quem usa todo dia, com que frequência, com que nível de familiaridade com o domínio}}
**Secundário**: {{quem consulta eventualmente — gestores, auditoria, outro órgão}}
**Não é público-alvo**: {{explicitar — evita features que ninguém pediu}}

## Proposta de valor

| Para | O que consegue |
|------|----------------|
| {{perfil}} | {{resultado concreto}} |

**Como saberemos que funcionou**: {{métrica observável — tempo de tramitação, retrabalho, número de exigências}}

## Restrições do contexto institucional

- **Norma aplicável**: {{leis, decretos, instruções normativas que o sistema precisa cumprir}}
- **Prazos legais**: {{prazos que o sistema precisa respeitar ou controlar}}
- **Integrações obrigatórias**: {{sistemas do órgão ou externos com que precisa conversar}}
- **Dados pessoais tratados**: {{quais categorias, com que base legal — ver LGPD}}
- **Acessibilidade**: {{padrão exigido}}
- **Sazonalidade**: {{picos de uso previsíveis, se houver}}

## Escopo

### Dentro
{{o que a versão atual entrega}}

### Fora (explícito)
{{o que não será feito e por quê — esta lista é o que segura o crescimento silencioso do escopo}}

## Princípios do produto

{{3 a 5 princípios que resolvem discussão sem precisar de reunião. Exemplos:}}
- Nenhuma tela pede ao usuário um dado que o sistema já tem.
- O sistema nunca bloqueia por falha de integração externa: degrada e avisa.
- Toda ação que altera dado de terceiro é auditável.

## Glossário do domínio

| Termo | Significado no órgão |
|-------|----------------------|
| {{termo}} | {{definição — use a definição da norma, não a intuitiva}} |

> O glossário evita a falha mais cara em sistema público: o desenvolvedor e a área usarem
> a mesma palavra para coisas diferentes.
