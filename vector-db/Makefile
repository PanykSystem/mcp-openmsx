# Makefile for MCP-openMSX project
.PHONY: all install embeddings prune

# Fall back to npm if pnpm not found
NPM := $(shell command -v pnpm >/dev/null 2>&1 && echo pnpm || echo npm)

ESC = $(shell printf '\033')
COL_WHITE = $(ESC)[1;37m
COL_ORANGE = $(ESC)[1;38:5:208m
COL_RESET = $(ESC)[0m

SERVER_DIR = mcp-server
MAKE = make -s --no-print-directory -C $(SERVER_DIR)

install:
	@$(NPM) ci

embeddings:
	@echo "$(COL_WHITE)#### Creating embeddings (GPU if available, else CPU)...$(COL_RESET)"
	OPENMSX_EMBED_PROVIDER=cuda npx tsx generate_embeddings.ts
	@echo "$(COL_ORANGE)⚠️  Consider a RAG prune with 'make prune'$(COL_RESET)"

prune:
	@echo "$(COL_WHITE)#### Pruning RAG$(COL_RESET)"
	@npx tsx -e 'import * as lancedb from "@lancedb/lancedb"; (async () => { const db = await lancedb.connect("../mcp-server/vector-db"); const tbl = await db.openTable("msxdocs"); console.log(await tbl.optimize({ cleanupOlderThan: new Date(), deleteUnverified: true })); })().catch((error) => { console.error(error); process.exit(1); });'

query:
	@echo "$(COL_WHITE)#### Querying VectorDB...$(COL_RESET)"
	#request the user to enter a query
	@read -p "Enter your query: " query; \
	echo "Query: $$query"; \
	npx tsx query_lancedb.ts "$$query"

# Checks

depcheck:
	@echo "$(COL_ORANGE)## depcheck...$(COL_RESET)"
	@depcheck || true

outdated:
	@echo "$(COL_ORANGE)## npm outdated...$(COL_RESET)"
	@$(NPM) outdated || true

npq:
	@echo "$(COL_ORANGE)## npq...$(COL_RESET)"
	@npq --dry-run || true

check: depcheck outdated npq
