<properties
   pageTitle="Creare un set di record e i record per una zona DNS con il portale di Azure | Microsoft Azure"
   description="Come creare i record host per DNS di Azure e i set di record e i record usando il portale di Azure"
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


# Creare record e set di record DNS con il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-recordset-cli.md)


Dopo aver creato la zona DNS, è necessario aggiungere i record DNS per il dominio. A tale scopo, è necessario comprendere i record e i set di record DNS.

## Informazioni sui set di record e sui record

### Informazioni sui record

Ogni record DNS ha un nome e un tipo.

Un nome di dominio completo include il nome della zona, mentre un nome relativo non lo include. Ad esempio, il nome del record relativo "www" nella zona "contoso.com" genera il nome di record completo "www.contoso.com".

>[AZURE.NOTE] Nel servizio DNS di Azure, i record vengono specificati usando nomi relativi.

I record possono essere di tipi diversi in base ai dati in essi contenuti. Il tipo più comune è un record "**A**", che esegue il mapping di un nome a un indirizzo IPv4. Un altro tipo è un record "**MX**", che esegue il mapping di un nome a un server di posta elettronica.

DNS di Azure supporta tutti i tipi di record DNS comuni: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT. Si noti che i record SPF devono essere creati con il tipo di record TXT. Per altre informazioni, vedere [questa pagina](http://tools.ietf.org/html/rfc7208#section-3.1).


### Informazioni sui set di record

In alcuni casi è necessario creare più record DNS con un determinato nome e tipo. Si supponga, ad esempio, che il sito Web www.contoso.com sia ospitato in due diversi indirizzi IP. Questa operazione richiede due diversi record A, uno per ogni indirizzo IP. Questo è un esempio di un set di record.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

DNS di Azure gestisce i record DNS usando il set di record. Un set di record è la raccolta di record DNS con lo stesso nome e tipo in una zona. La maggior parte dei set di record contiene un singolo record, ma non sono rari esempi come quello precedente, in cui un set di record contiene più di un record

I set di record di tipo SOA e CNAME sono un'eccezione: gli standard DNS non consentono più record con lo stesso nome per questi tipi.

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client prima che venga eseguita nuovamente la query. Nell'esempio precedente, il valore TTL è 3600 secondi o 1 ora. Il valore TTL viene specificato per il set di record, non per ogni record, quindi lo stesso valore viene usato per tutti i record all'interno di tale set di record.

#### Set di record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Questi dati vengono restituiti per le query con un nome corrispondente (a meno che non esiste una corrispondenza più vicina da un set di record non jolly). I set di record con caratteri jolly sono supportati per tutti i tipi di record tranne NS e SOA.

Per creare un set di record con caratteri jolly, utilizzare il nome del set di record "*", o un nome con la prima etichetta è "*", ad esempio, "*.foo".

#### Set di record CNAME

I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, non è possibile creare contemporaneamente un record CNAME con il nome relativo "www" e un record A con il nome relativo '"www". Dal momento che il vertice della zona (name = ‘@’) contiene sempre i set di record NS e SOA creati quando viene creata la zona, ciò significa che non è possibile creare un set di record CNAME al vertice della zona. Questi vincoli sono causati dagli standard DNS, non sono limitazioni di DNS di Azure.


## Per creare un nuovo set di record e un record

L'esempio seguente fornisce indicazioni su come creare un set di record e record con il portale di Azure. Viene usato il tipo di record DNS 'A'.

1. Accedere al portale di Azure.

2. Passare al pannello **Zona DNS** in cui creare un set di record.

3. Nella parte superiore del pannello della zona DNS fare clic su **Set di record** per aprire il pannello **Aggiungi set di record**.
 
	![nuovo set di record](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Nel pannello **Aggiungi set di record** assegnare un nome al set di record, ad esempio, "**www**".
  
	![aggiungere un set di record](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. In Tipo selezionare il tipo di record da creare. Ad esempio, **A**.

6. Impostare **TTL**. L'impostazione predefinita nel portale è di 1 ora.

7. Aggiungere gli indirizzi IP, un indirizzo IP per riga. Se si usa il nome del set di record suggerito e il tipo di record precedente, vengono aggiunti gli indirizzi IP IPv4 al record A del set di record www.

8. Dopo avere aggiunto gli indirizzi IP, fare clic su **OK** nella parte inferiore del pannello. Verrà creato il set di record DNS.

## Testare la zona DNS usando gli strumenti DNS


Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server di nomi per la zona vengono visualizzati nel riquadro Informazioni di base del pannello della zona DNS. Per altre informazioni, vedere l'articolo [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

È possibile testare la zona DNS usando strumenti DNS come nslookup, DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).


## Passaggi successivi

Per altre informazioni su DNS di Azure, vedere la [Panoramica di DNS di Azure](dns-overview.md). Per informazioni sull'automazione di DNS, vedere [Creazione di zone e set di record DNS con .NET SDK](dns-sdk.md).

<!---HONumber=AcomDC_0406_2016-->