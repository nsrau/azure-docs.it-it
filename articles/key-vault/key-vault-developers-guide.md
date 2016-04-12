<properties
   pageTitle="Guida per gli sviluppatori dell'insieme di credenziali delle chiavi | Microsoft Azure"
   description="Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="bruceper" />

# Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Con l'insieme di credenziali delle chiavi è possibile accedere in modo sicuro ai dati sensibili all'interno delle applicazioni in modo che:

- Le chiavi e i segreti vengano protetti, senza dover scrivere manualmente il codice e possano essere usati dalle applicazioni.
- I clienti possano avere e gestire autonomamente le rispettive chiavi in modo che lo sviluppatore possa concentrarsi su altre attività, ad esempio fornire le principali funzionalità del software. In questo modo le applicazioni non saranno responsabili dei segreti e delle chiavi tenant dei clienti.
- L'applicazione possa usare le chiavi per la firma e la crittografia, ma tenga la gestione della chiave esterna all'applicazione in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita.

Per altre informazioni generali sull'insieme di credenziali delle chiavi di Azure, vedere l'articolo contenente [informazioni sull'insieme di credenziali delle chiavi](key-vault-whatis.md)

## Creazione e gestione di insiemi di credenziali delle chiavi

Prima di usare l'insieme di credenziali delle chiavi di Azure nel codice, è possibile creare e gestire insiemi di credenziali tramite REST, modelli di Resource Manager, PowerShell o l'interfaccia della riga di comando, come descritto negli articoli seguenti:

- [Creare e gestire insiemi di credenziali chiave con REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Creare e gestire insiemi di credenziali chiave con PowerShell](key-vault-get-started.md)
- [Creare e gestire insiemi di credenziali chiave con l’interfaccia della riga di comando](key-vault-manage-with-cli.md)
- [Creare un insieme di credenziali delle chiavi e aggiungere un segreto tramite un modello di Azure Resource Manager](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Le operazioni sugli insiemi di credenziali delle chiavi vengono autenticate tramite AAD e autorizzate tramite i criteri di accesso dell'insieme di credenziali delle chiavi, definiti per ogni insieme di credenziali.

## Codifica con l'insieme di credenziali delle chiavi

Il sistema di gestione dell'insieme di credenziali delle chiavi per i programmatori è costituito da diverse interfacce, che hanno come base REST, [Informazioni di riferimento sulle API REST dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Gli utenti per i quali l'autorizzazione ha esito positivo possono eseguire le operazioni seguenti:

- Gestire le chiavi di crittografia utilizzando [Crea](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importa](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Aggiorna](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Elimina](https://msdn.microsoft.com/library/azure/dn903611.aspx) e altre operazioni

- Gestire i segreti usando operazioni quali [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx) e [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx)

- Utilizzare le chiavi di crittografia con le operazioni [Accedi](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verifica](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Crittografa](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrittografa](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Per l'uso con l'insieme di credenziali delle chiavi sono disponibili gli SDK seguenti:

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentazione su .NET SDK](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentazione su Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacchetto di .NET SDK](https://azure.microsoft.com/documentation/api/)|[Pacchetto Node.js SDK](https://www.npmjs.com/package/azure-keyvault)|


Per esempi completi relativi all'uso dell'insieme di credenziali delle chiavi con le applicazioni, vedere:

- L'applicazione di esempio .NET *HelloKeyVault* e un esempio di servizio Web di Azure. [Esempi di codice di insieme di credenziali chiave di Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- L'esercitazione relativa all'uso dell'insieme di credenziali delle chiavi di Azure da un'applicazione Web in Azure. [Usare l'insieme di credenziali chiave di Azure da un'applicazione Web](key-vault-use-from-web-application.md)

## Procedure

Gli articoli e gli scenari seguenti forniscono indicazioni specifiche dell'attività:

- [Come generare e trasferire chiavi protette HSM per l’insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md)
- [Passare valori sicuri (ad esempio password) durante la distribuzione](../resource-manager-keyvault-parameter.md)
- Per indicazioni specifiche dell'attività per l'integrazione e l'uso dell'insieme di credenziali delle chiavi con Azure, vedere [esempi di modelli di Azure Resource Manager di Ryan Jones per l'insieme di credenziali delle chiavi](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)

## Supporto di librerie

- La [libreria principale dell'insieme di credenziali delle chiavi di Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fornisce le interfacce `IKey` e `IKeyResolver` per l'individuazione delle chiavi dagli identificatori e l'esecuzione di operazioni con le chiavi.

- Le [estensioni dell'insieme di credenziali delle chiavi di Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fornisce funzionalità estese per l'insieme di credenziali delle chiavi di Azure.

<!---HONumber=AcomDC_0309_2016-->