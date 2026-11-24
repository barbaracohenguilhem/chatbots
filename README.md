# Família SP — protótipo executável local

Este projeto é um MVP de análise de PDFs fornecidos por usuário autorizado. **Não é um sistema de produção**, não acessa TJSP/e-SAJ/eproc, não tem OCR, controle multiusuário, pesquisa jurisprudencial online, extração de prazos ou auditoria independente automatizada.

## Iniciar

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# Edite .env com chave de API, token forte e diretório privado
set -a; source .env; set +a
uvicorn app:app --host 127.0.0.1 --port 8000
```

Acesse `http://127.0.0.1:8000/docs` e informe o cabeçalho `x-agent-token` nas chamadas. Exemplos:

```bash
curl -X POST http://127.0.0.1:8000/documents -H "x-agent-token: $FAMILY_AGENT_TOKEN" -F 'file=@autos.pdf'
curl -X POST http://127.0.0.1:8000/analyze -H "x-agent-token: $FAMILY_AGENT_TOKEN" -H 'Content-Type: application/json' -d '{"question":"Quais decisões sobre guarda aparecem nos documentos?"}'
```

## Segurança e limitações

- Dados sensíveis são enviados à API do modelo durante a análise. Verifique contratos, política de retenção e base legal antes de enviar autos reais.
- Não exponha a porta na internet. O token único é apenas uma barreira mínima de MVP.
- O armazenamento local não está criptografado pelo aplicativo. Use disco criptografado e backups seguros.
- PDFs escaneados exigem OCR; a API informa quantas páginas ficaram sem texto.
- O ranking lexical é simplificado e pode deixar de recuperar informações decisivas.
- Antes de uso real: autenticação individual, autorização por caso, isolamento de famílias, criptografia, auditoria, revisão de LGPD, validação de citações e testes.
- Os arquivos `.env` e `private_data/` devem permanecer fora do controle de versão.

## Evolução recomendada

1. Ingestão de DOCX, e-mails e OCR com conferência visual.
2. Entidades e eventos estruturados com relações e proveniência.
3. Busca híbrida (BM25 + embeddings) com reranking e checagem de cobertura.
4. Pesquisa oficial de jurisprudência com registro de URL, órgão, data e status.
5. Fluxo investigador → pesquisador → contraditor → auditor, com aprovações humanas.
6. Alertas de prazos apenas após validação profissional de intimação e calendário.
7. Testes com casos anonimizados e avaliação de precisão, recall e taxa de citação inválida.
