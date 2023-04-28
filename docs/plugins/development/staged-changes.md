# Mudanças Pendentes

!!! danger Característica Experimental

    Essa característica está sob desenvolvimento ativo e é considerada experimental em sua natureza. Seu uso em produção é fortemente não recomendada neste momento.

!!! note Observação

    Essa característica foi introduzida na versão v3.4.

O NetBox fornece uma API programática para permitir a criação, modificação e remoção de objetos sem realmente salvar (fazer o commit) dessas mudanças pro banco de dados ativo. Isso pode ser utilizado para performar um conjunto de operações em grupo do tipo "dry run", ou preparar um grupo de mudanças para aprovação administrativa, por exemplo.

Para começar com mudanças pendentes (staging changes), primeiro crie uma [branch](../../models/extras/branch.md) (ramo):

```python
from extras.models import Branch

branch1 = Branch.objects.create(name='branch1')
```

Então, ative a branch usando o gerenciador de contexto `checkout()` e comece suas mudanças. Isso inicia uma nova transação no banco de dados.

```python
from extras.models import Branch
from netbox.staging import checkout

branch1 = Branch.objects.get(name='branch1')
with checkout(branch1):
    Site.objects.create(name='New Site', slug='new-site')
    # ...
```

Ao sair do contexto, a transação do banco de dados é automaticamente revertida (rolled back) se suas mudanças gravadas como [staged changes](../../models/extras/stagedchange.md) são revertidas. Entrar novamente na branch, irá criar uma nova transação do banco de dados e automaticamente aplicará qualquer mudança pendente associada com essa branch.

Para aplicar as mudanças dentro da branch, chame o método da branch `commit()`:

```python
from extras.models import Branch

branch1 = Branch.objects.get(name='branch1')
branch1.commit()
```

Salvar (commiting) uma branch é uma operação all-or-none (tudo ou nada): qualquer exceção irá reverter o grupo inteiro de mudanças. Depois de salvar (realizar o commit) com sucesso de uma branch, todos os objetos `StagedChanges` serão automaticamente deletados (no entanto, a branch em si permanecerá e poderá ser reutilizada).
