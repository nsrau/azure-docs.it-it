1. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. Per altre informazioni sulla registrazione, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli).

  ```azurecli
  az login
  ```
2. Se si hanno più sottoscrizioni Azure, elencare le sottoscrizioni per l'account.

  ```azurecli
  az account list --all
  ```
3. Specificare la sottoscrizione da usare.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```