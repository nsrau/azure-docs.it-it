<properties
	pageTitle="Rete CDN : Come limitare l'accesso al contenuto in base al paese"
	description="Quando un utente richiede il contenuto, per impostazione predefinita il contenuto viene servito indipendentemente dalla località dell'utente che effettua la richiesta. In alcuni casi, è possibile limitare l'accesso al contenuto in base al paese. In questo argomento viene illustrato come utilizzare la funzionalità **Filtro di paese ** per configurare il servizio per consentire o bloccare l'accesso in base al paese."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

#Limitare l'accesso al contenuto in base al paese

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Quando un utente richiede il contenuto, per impostazione predefinita il contenuto viene servito indipendentemente dalla località dell'utente che effettua la richiesta. In alcuni casi, è possibile limitare l'accesso al contenuto in base al paese. In questo argomento viene illustrato come utilizzare la funzionalità **filtro di paese** per configurare il servizio per consentire o bloccare l'accesso in base al paese.

>[AZURE.NOTE]Una volta impostata la configurazione, verrà applicata a tutti gli endpoint della **rete CDN di Azure fornita da Verizon** nella sottoscrizione.

Per informazioni su considerazioni relative alla configurazione di questo tipo di restrizioni, vedere la sezione [Considerazioni](cdn-restrict-access-by-country.md#considerations) alla fine dell'argomento.

![Filtro di paese](./media/cdn-filtering/cdn-country-filtering.png)


##Passaggio 1: definire il percorso di directory

Quando si configura un filtro di paese, è necessario specificare il percorso relativo della posizione a cui gli utenti potranno o meno accedere. È possibile applicare il filtro di paese per tutti i file con "/" o le cartelle selezionate specificando i percorsi di directory.

Esempio di filtro di percorso di directory:

	/                                 
	/Photos/
	/Photos/Strasbourg

##Passaggio 2: definire l'azione Blocca o Consenti

**Blocca**: agli utenti dei paesi specificati verrà negato l'accesso alle risorse richieste da quel percorso ricorsivo. Se non sono state definite altre opzioni di filtro di paese per tale percorso, tutti gli altri utenti saranno autorizzati ad accedere.

**Consenti**: solo agli utenti dei paesi specificati verrà autorizzato l'accesso alle risorse richieste da quel percorso ricorsivo.

##Passaggio 3: definire i paesi

Selezionare i paesi che si desidera bloccare o consentire per il percorso. Per ulteriori informazioni, vedere l’argomento [Codici paese](cdn-country-codes.md).

Per esempio, la regola di blocco /Photos/Strasbourg/ filtrerà i file tra cui:

	http://az123456.azureedge.net/Photos/Strasbourg/1000.jpg
	http://az123456.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##Codici paese

La funzionalità di **filtro di paese** utilizza i codici paese per definire i paesi da cui una richiesta viene consentita o bloccata per una directory protetta. Sono disponibili i codici paese in [questo](cdn-country-codes.md) argomento. Se si specifica "UE" (Europa) o "PA" (Asia/Pacifico), un sottoinsieme di indirizzi IP che provengono da paesi in quelle aree geografiche verrà bloccato o consentito.


##<a id="considerations"></a>Considerazioni

- Per rendere effettive le modifiche alla configurazione del filtro di paese potrebbe essere necessaria anche un’ora.
- Questa funzionalità non supporta i caratteri jolly (ad esempio, ‘*’).
- La configurazione del filtro di paese associata al percorso relativo viene applicata in modo ricorsivo a tale percorso.
- Può essere applicata solo una regola allo stesso percorso relativo (non è possibile creare più filtri di paese che puntano allo stesso percorso relativo). Tuttavia, una cartella potrebbe avere più filtri di paese. Ciò è dovuto alla natura ricorsiva dei filtri di paese. In altre parole, una sottocartella di una cartella configurata in precedenza può essere assegnata a un filtro di paese diverso.

<!---HONumber=AcomDC_0518_2016-->