# Validator

Uma biblioteca de validação para structs no Go, que utiliza regras definidas programaticamente, sem a necessidade de tags. Este pacote é ideal para quem busca flexibilidade e reutilização de lógica de validação em diversos contextos de uma aplicação.

## 📋 Funcionalidades

- Validação baseada em regras programáticas (sem tags).
- Suporte a validações customizadas.
- Mensagens de erro associadas aos campos.
- Compatível com projetos complexos e escaláveis.

## 🛠️ Instalação

Instale o pacote usando:

```bash
go get github.com/Marlliton/validator
```

## 🚀 Uso Básico

Veja um exemplo de como criar e validar uma struct usando o pacote:

### Definição e Validação

```go
package main

import (
	"fmt"

	"github.com/Marlliton/validator"
	"github.com/Marlliton/validator/rule"
	"github.com/Marlliton/validator/validator_error"
)

type User struct {
	Name  string
	Email string
	Age   int
}

func (u *User) Validate() ([]*validator_error.ValidatorError, bool) {
	v := validator.New()

	v.Add("Name", rule.Rules{
		rule.Required(),
		rule.MinLength(3),
		rule.MaxLength(50),
	})
	v.Add("Email", rule.Rules{
		rule.Required(),
		rule.ValidEmail(),
	})
	v.Add("Age", rule.Rules{
		rule.MinValue(18),
	})

	errs := v.Validate(*u)
	if len(errs) == 0 {
		return nil, true
	}
	return errs, false
}

func main() {
	user := User{Name: "Jo", Email: "invalid_email", Age: 15}
	if errs, ok := user.Validate(); !ok {
		fmt.Println("Erros de validação:")
		for _, err := range errs {
			fmt.Printf("Campo: %s, Mensagem: %s\n", err.Field, err.Message)
		}
	} else {
		fmt.Println("Tudo válido!")
	}
}
```

### Regras de Validação Suportadas

O pacote oferece uma variedade de regras que podem ser aplicadas aos campos. Exemplos:

- `rules.Required()`: O campo é obrigatório.
- `rules.MinLength(n)`: Comprimento mínimo.
- `rules.MaxLength(n)`: Comprimento máximo.
- `rules.ExactLength(n)`: Comprimento exatamente igual a "n".
- `rules.ValidEmail()`: Valida um email.
- `rules.ValidPhoneNumber()`: Valida um telefone no formato [e164](https://en.wikipedia.org/wiki/E.164).
- `rules.MinValue(n)`: Valor mínimo permitido.
- `rules.MaxValue(n)`: Valor máximo permitido.

Você pode combinar essas regras em diferentes campos.

### Validações Personalizadas

Crie suas próprias validações passando uma função personalizada como regra:

```go
v.Add("CustomField", rule.Rules{
	func(fieldName string, value interface{}) *validator_error.ValidatorError {
		if value.(int)%2 != 0 {
			return &validator_error.ValidatorError{
				Field:   fieldName,
				Message: "O número deve ser par",
			}
		}
		return nil
	},
})
```

## 📚 Exemplos

- Exemplos de uso disponíveis no diretório [`examples`](./examples).

## 🛡️ Contribuindo

Contribuições são bem-vindas! Abra um issue para relatar problemas ou envie um pull request com melhorias. Certifique-se de seguir as diretrizes no arquivo [CONTRIBUTING.md](./CONTRIBUTING.md).

## Itens a Fazer

- [ ] Permitir que o usuário forneça sua próprias mensagens customizadas

## 📄 Licença

Este projeto é distribuído sob a licença [MIT](./LICENSE).
