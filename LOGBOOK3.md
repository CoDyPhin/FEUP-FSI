# Trabalho realizado na Semana #3

## Identificação

- *Cross-Site Scripting* (XSS)  permite executar código *JavaScript* através do parâmetro *lastname*, no *browser* da vítima.
- Esta vulnerabilidade está presente num formulário da **spotweb 1.5.1** (https://github.com/spotweb/spotweb).
- Código com sanitização incorreta: `value='<?php echo htmlspecialchars($form['lastname']); ?>'/></td>`

## Catalogação

- A vulnerabilidade foi encontrada por um utilizador da *spotweb* em setembro de 2021.
-  Este *user* criou um *issue* no *github* que descreve e explica como explorar esta falha de segurança.
- Não há informação sobre qualquer recompensa atribuída ao utilizador que descobriu esta vulnerabilidade.
- De acordo com o **Security-Database Scoring CVSS v2**, este CVE apresenta um nível de gravidade de 4.3 (médio) e de acordo com o **Security-Database Scoring CVSS v3** um nível de gravidade de 6.1 (médio).  


## Exploit

- De modo a explorar esta vulnerabilidade, o atacante injeta o código malicioso no *browser* da vítima, sendo que, neste caso, se foca no parâmetro do *lastname*.
- Exemplo: 
    `<html>`
    `<form method="POST" action="http://[localhost]/spotweb/install.php?page=4">`
     `<input name='settingsform[lastname]' value="1' onclick='alert(1)">`
    `<button>Send my greetings</button>`
    `</form>`
    `</html>`


## Ataques

- Ao explorar esta vulnerabilidade, poderão ser executadas ações não autorizadas, tais como acesso não permitido a dados, roubo da sessão, *denial of service* (DoS), entre outros.
- Não existem relatos de ataques bem sucedidos através deste CVE.

