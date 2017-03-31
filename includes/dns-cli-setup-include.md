## <a name="set-up-azure-cli-for-azure-dns"></a>Configurare l'interfaccia della riga di comando di Azure per DNS di Azure

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* Installare la versione più recente dell'interfaccia della riga di comando di Azure, disponibile per Windows, Linux o Mac. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Aprire una finestra della console ed eseguire l'autenticazione con le credenziali. Per altre informazioni, vedere [Accedere ad Azure dall'interfaccia della riga di comando di Azure](../articles/xplat-cli-connect.md).

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Passare alla modalità interfaccia della riga di comando

DNS di Azure usa Gestione risorse di Azure. Assicurarsi di passare alla modalità interfaccia della riga di comando per usare i comandi di Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Selezionare la sottoscrizione

Controllare le sottoscrizioni per l'account.

```azurecli
azure account list
```

Scegliere le sottoscrizioni ad Azure da usare.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dato che tutte le risorse DNS sono globali, non regionali, la scelta del percorso del gruppo di risorse non ha alcun impatto sul servizio DNS di Azure.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registrare il provider di risorse

Il servizio DNS di Azure viene gestito dal provider di risorse Microsoft.Network. Per poter usare il servizio DNS di Azure, la sottoscrizione di Azure deve essere registrata per l'uso di questo provider di risorse. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

```azurecli
azure provider register --namespace Microsoft.Network
```

