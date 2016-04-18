<properties
   pageTitle="Come creare e gestire una zona DNS nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare le zone DNS per DNS di Azure. Si tratta di una guida dettagliata per creare e gestire il primo DNS e iniziare a ospitare il dominio DNS con il portale di Azure."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="cherylmc"/>

# Creazione e gestione di una zona DNS nel portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)



Questo articolo illustra i passaggi per creare una zona DNS con il portale di Azure. È anche possibile creare una zona DNS con PowerShell o l'interfaccia della riga di comando.

Il dominio "contoso.com" può contenere una serie di record DNS, ad esempio "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito Web). Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio è necessario prima di tutto creare una zona DNS. Qualsiasi record DNS creato per un particolare dominio si troverà all'interno di una zona DNS per il dominio.

### <a name="names"></a>Informazioni sui nomi delle zone DNS
 
- Il nome della zona deve essere univoco all'interno del gruppo di risorse e la zona non deve esistere già, altrimenti l'operazione non riesce.

- Lo stesso nome di zona può essere usato nuovamente in un gruppo di risorse diverso o in un'altra sottoscrizione Azure. Se più zone condividono lo stesso nome, a ogni istanza verranno assegnati diversi indirizzi del server dei nomi e può essere delegata solo un'istanza dal dominio padre. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](#delegate).

### Informazioni sui tag per DNS di Azure


I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

È possibile aggiungere tag nel portale di Azure con il pannello **Impostazioni** per la zona DNS.


## Creare una zona DNS

1. Accedere al portale di Azure

2. Nel menu Hub fare clic su **New > Rete >**, quindi scegliere **Zona DNS** per aprire il pannello della zona DNS.
 
	![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. Nella parte inferiore del pannello **Zona DNS** fare clic su **Crea**. Viene aperto il pannello **Crea zona DNS**.

	![Crea zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. Nel pannello **Crea zona DNS** assegnare un nome alla zona DNS. ad esempio *contoso.com*. Vedere [Informazioni sui nomi delle zone DNS](#names) nella sezione precedente.

5. Successivamente, specificare il gruppo di risorse da usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già esistente.

6. Specificare il percorso del gruppo di risorse nell'elenco a discesa **Percorso**. Si noti che questa impostazione si riferisce al percorso del gruppo di risorse, non al percorso della zona DNS. La risorsa di zona DNS effettiva è automaticamente "globale" e non può, o deve, essere specificata nel portale.

7. È possibile lasciare selezionata la casella di controllo **Aggiungi al dashboard** per trovare facilmente la nuova zona nel dashboard. Fare quindi clic su **Crea**.

	![Aggiungi al dashboard](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. Dopo aver fatto clic su Crea, la nuova zona viene configurata nel dashboard.

	![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. Dopo aver creato la nuova zona, nel dashboard viene aperto il pannello per la nuova zona.


## Visualizzare i record di zona DNS

La creazione di una zona DNS comporta anche la creazione dei record seguenti:

- Il record Origine di autorità (SOA), presente nella radice di ogni zona DNS.
- I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

È possibile visualizzare i record dal portale di Azure

1. Dal pannello **Zona DNS** fare clic su **Tutte le impostazioni** per aprire il **pannello Impostazioni** per la zona DNS. 

	![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. Nella parte inferiore del riquadro Informazioni di base è possibile visualizzare i set di record per la zona DNS.


	![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)



## Eliminare una zona DNS

È possibile eliminare la zona DNS direttamente dal portale. Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

1. Individuare il pannello **Zona DNS** per la zona da eliminare, quindi fare clic su **Elimina** nella parte superiore del pannello.
 
2. Viene visualizzato un messaggio che informa che è necessario eliminare tutti i set di record, tranne i record NS e SOA creati automaticamente. Se i set di record sono stati eliminati, fare clic su **Sì**. Si noti che quando si elimina una zona DNS dal portale, non viene eliminato il gruppo di risorse associato alla zona DNS.


## Passaggi successivi

Dopo aver creato la zona DNS, vedere [Introduzione alla creazione di set di record e record](dns-getstarted-create-recordset-portal.md), [Come gestire le zone DNS](dns-operations-dnszones.md) e [Come gestire i record DNS](dns-operations-recordsets-portal.md).

<!---HONumber=AcomDC_0406_2016-->