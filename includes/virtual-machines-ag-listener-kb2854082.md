Se i server nel cluster stanno eseguendo Windows Server 2008 R2 o Windows Server 2012, è necessario quindi verificare che l'hotfix [KB2854082](http://support.microsoft.com/kb/2854082) sia installato su ognuno dei server locali o sulle VM di Azure che fanno parte del cluster. Qualsiasi server o macchina virtuale che si trova nel cluster, ma non nel gruppo di disponibilità, deve avere installato tale hotfix.

Nella sessione desktop remota per ciascuno dei nodi del cluster, scaricare [KB2854082](http://support.microsoft.com/kb/2854082) in una directory locale. Quindi, installare l'hotfix in ogni nodo del cluster in modo sequenziale. Se il servizio cluster è in esecuzione nel nodo del cluster, il server viene riavviato al termine dell'installazione dell'hotfix.

> [!WARNING]
> L'arresto del servizio cluster o il riavvio del server influiscono sull'integrità del quorum del cluster e del gruppo di disponibilità e possono portare il cluster offline. Per mantenere la disponibilità elevata del cluster durante l'installazione, verificare quanto segue:
> 
> * Il cluster è in stato di quorum ottimale. 
> * Prima dell'installazione dell'hotfix in qualsiasi nodo, tutti i nodi del cluster sono online.
> * Prima di installare l'hotfix in altri nodi del cluster, è necessario consentire il completamento dell'esecuzione dell'installazione dell'hotfix in un nodo, incluso il riavvio completo del server.
> 
> 

