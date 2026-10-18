

Decisões
1. Vamos tirar a aprovação manual por conta que a integração funciona perfeitamente com o toughtspot. 
2. Não terá mais suspensão (porque o admin gostaria de suspender um usuário).
3. Não vamos ter integração com google enem outro provedor para login.
4. A parte de agenda não terá gravação em bacno, tudo será carregado a partir da Api do sympla. Não tem mais as tabelas de agenda.    


1.  Sem aprovação manual
- US-01.10 (Should), US-02.11 (Must), status CONCEDIDO_MANUALMENTE
- O caso que ela cobre é a base do PMI atrasada, não falha da integração. Atualizar o ÉP-01 e o ÉP-02, e tirar “Conceder filiação” e o comprovante do protótipo

2. Sem suspensão
-  US-02.10 c4–c5, US-01.04 c6, filtro e indicador “Contas suspensas” (US-02.09)
- O Admin fica sem forma de bloquear uma conta abusiva. O protótipo ainda tem o botão “Suspender conta”

3. Sem Google
- US-01.03 (Should)
- Sem efeito colateral; a senha passa a ser sempre obrigatória. Tirar “Continuar com o Google” do protótipo

4. Agenda sem gravação em banco

5. Preferências / consentimento
- US-02.04 (Must), US-02.08, aceite do termo (RN-01.01.5), crédito só com gamificação ativa, quem aparece na vitrine

6. PECA_CONQUISTADA e CREDITO
- US-08.02, 08.04, 08.05
- qtd_pecas_obtidas diz quantas peças, não quais. Sem isso não dá para sortear “uma posição que ainda não tenho”, entregar só as peças conquistadas, nem mostrar o saldo

7. Vigência e status do benefício
- US-04.02, 04.05, 04.06
- vigencia_inicio, vigencia_fim, situação e link_parceiro

8. Registro do resgate
- RN-04.03.2, RN-04.06.3
- Código entregue e situação da filiação no momento. Sem isso, se o Admin trocar o cupom, o histórico de quem já resgatou mostra o código novo

9. Dados de verificação da filiação
- US-01.09, 01.11, 02.07
- data_ultima_verificacao (revalida após 7 dias), data_solicitacao, motivo_pendencia

10. Tentativas de vinculação
- US-01.13 c5–c6
- O limite por hora pode ficar no Redis; a tela do Admin e o alerta de “3 contas no mesmo PMI ID”, não

11. Auditoria
- Mais de 15 regras, incluindo “a versão anterior fica na auditoria” (US-04.06, 07.05)
- Uma entidade genérica: quem, ação, alvo, antes, depois, data

12. Logo da vaga
- US-07.02 c4 (o Não-Filiado só vê os logos)
