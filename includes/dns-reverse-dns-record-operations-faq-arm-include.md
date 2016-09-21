<BR>
## Domande frequenti 
### Quanto costano i record DNS inversi?
Sono gratuiti! Non sono previsti costi aggiuntivi per i record DNS inversi o le query.
### I record DNS inversi verranno risolti da Internet?
Sì. Dopo aver impostato la proprietà DNS inverso per l'indirizzo IP pubblico, Azure gestisce tutte le deleghe DNS e le zone DNS richieste per assicurare che il record DNS inverso si risolva per tutti gli utenti di Internet.
### Verrà creato un record DNS inverso predefinito per gli indirizzi IP pubblici?
No. Il DNS inverso sarà una funzionalità che prevede il consenso esplicito. Se si sceglie di non configurare alcun record DNS inverso predefinito, non ne verrà creato alcuno.
### Qual è il formato per il nome di dominio completo (FQDN)?
I nomi di dominio completi sono specificati seguendo l’ordine in avanti e devono terminare con un punto (ad esempio, "app1.contoso.com.").
### Cosa accade se i controlli di convalida per il DNS inverso specificati danno esito negativo?
Se i controlli di convalida per il DNS inverso danno esito negativo, l'operazione di gestione del servizio non andrà a buon fine. Correggere il valore DNS inverso come richiesto e riprovare.
### È possibile gestire il DNS inverso per il sito Web di Azure?
Il DNS inverso non è supportato per i siti Web di Azure. Il DNS inverso è supportato per le macchine virtuali di Azure.
### È possibile configurare più record DNS inversi per l'indirizzo IP pubblico?
No. Azure supporta un singolo record DNS inverso per ogni indirizzo IP pubblico. Ogni indirizzo IP pubblico tuttavia può avere un proprio record DNS inverso.
### È possibile configurare un record DNS inverso per l'indirizzo IP pubblico senza una proprietà domainNameLabel specificata?
No. Per sfruttare i record DNS inversi per indirizzi IP pubblici, è necessario specificare la proprietà domainNameLabel.
### È possibile ospitare zone ARPA per gli IP assegnati da Azure sul DNS di Azure all'interno della propria sottoscrizione o sui propri server DNS autorevoli?
No. Azure non supporta la delega successiva delle zone ARPA. Azure ospita le zone ARPA per tutti gli indirizzi IP disponibili e consente ai clienti di creare record DNS inversi all'interno di queste zone ARPA.
### È possibile ospitare zone ARPA per i blocchi IP assegnati dall’ISP sul DNS di Azure?
No. Il DNS di Azure attualmente non supporta i record DNS inversi nelle zone DNS dei clienti.
### È possibile inviare messaggi di posta elettronica a domini esterni dai servizi di calcolo di Azure?
No. Come spiegato [qui](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/), i servizi di calcolo di Azure non supportano l'invio di messaggi di posta elettronica a domini esterni.

<!---HONumber=AcomDC_0907_2016-->