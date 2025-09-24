<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>💳 Meus Gastos Sincronizados</title>
    <style>
        :root {
            --primary: #007AFF;
            --success: #34C759;
            --danger: #FF3B30;
            --bg: #F2F2F7;
            --card: #FFFFFF;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, sans-serif;
            background: var(--bg);
            padding: 20px;
            padding-bottom: 100px;
        }
        
        .card {
            background: var(--card);
            border-radius: 14px;
            padding: 20px;
            margin-bottom: 15px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        
        input, select {
            width: 100%;
            padding: 14px;
            margin: 8px 0;
            border: 1px solid #ddd;
            border-radius: 10px;
            font-size: 16px;
        }
        
        button {
            width: 100%;
            padding: 16px;
            border: none;
            border-radius: 10px;
            font-size: 17px;
            font-weight: 600;
            margin: 5px 0;
            cursor: pointer;
        }
        
        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        
        .status {
            padding: 12px;
            border-radius: 10px;
            margin: 10px 0;
            text-align: center;
            font-weight: 600;
        }
        
        .online { background: #d4ffd4; color: #155724; }
        .offline { background: #fff0d4; color: #856404; }
    </style>
</head>
<body>
    <div class="card">
        <h1>💳 Meus Gastos</h1>
        <div class="status offline" id="status">🔄 Iniciando...</div>
    </div>

    <div class="card">
        <h3>➕ Novo Gasto</h3>
        <input type="date" id="data">
        <input type="text" id="descricao" placeholder="Descrição">
        <select id="categoria">
            <option>Alimentação</option>
            <option>Transporte</option>
            <option>Compras</option>
            <option>Lazer</option>
        </select>
        <input type="number" id="valor" placeholder="Valor R$" step="0.01">
        
        <button class="btn-primary" onclick="adicionarGasto()">💾 Salvar</button>
    </div>

    <div class="card">
        <h3>📊 Resumo</h3>
        <p>Total: R$ <span id="total">0,00</span></p>
        <p>Gastos: <span id="contador">0</span></p>
        
        <button class="btn-success" onclick="sincronizar()">🔄 Sincronizar</button>
    </div>

    <div class="card">
        <h3>📝 Gastos</h3>
        <div id="lista"></div>
    </div>

    <script>
        // ✅ URL DO SEU APPS SCRIPT - AGORA VAI FUNCIONAR!
        const API_URL = 'https://script.google.com/macros/s/AKfycbxQ0cpI8Dwg6FjyVjXuU3smhXgKpzBJuN76JrhIJsfwi6fH-L2QlnVvw_oMnpJ9cN2clQ/exec';
        
        let gastos = [];
        
        // Inicializar
        document.addEventListener('DOMContentLoaded', function() {
            const hoje = new Date();
            document.getElementById('data').value = hoje.toISOString().split('T')[0];
            carregarDados();
        });
        
        async function carregarDados() {
            // Primeiro tenta da nuvem
            try {
                await carregarNuvem();
            } catch (error) {
                // Se falhar, usa dados locais
                carregarLocal();
            }
            atualizarInterface();
        }
        
        async function carregarNuvem() {
            const response = await fetch(API_URL);
            const dados = await response.json();
            
            if (Array.isArray(dados)) {
                gastos = dados;
                localStorage.setItem('gastos', JSON.stringify(gastos));
                atualizarStatus('✅ Dados da nuvem carregados', 'online');
            }
        }
        
        function carregarLocal() {
            const dados = localStorage.getItem('gastos');
            gastos = dados ? JSON.parse(dados) : [];
            atualizarStatus('📱 Dados locais', 'offline');
        }
        
        async function adicionarGasto() {
            const gasto = {
                id: Date.now(),
                data: document.getElementById('data').value,
                descricao: document.getElementById('descricao').value,
                categoria: document.getElementById('categoria').value,
                valor: parseFloat(document.getElementById('valor').value),
                device: 'GitHubPages'
            };
            
            if (!gasto.descricao || !gasto.valor) {
                alert('Preencha todos os campos!');
                return;
            }
            
            // Adiciona localmente
            gastos.unshift(gasto);
            localStorage.setItem('gastos', JSON.stringify(gastos));
            atualizarInterface();
            
            // Limpa campos
            document.getElementById('descricao').value = '';
            document.getElementById('valor').value = '';
            
            // Tenta sincronizar
            try {
                await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(gasto)
                });
                atualizarStatus('✅ Salvo na nuvem!', 'online');
            } catch (error) {
                atualizarStatus('📱 Salvo localmente', 'offline');
            }
        }
        
        async function sincronizar() {
            try {
                await carregarNuvem();
                atualizarInterface();
            } catch (error) {
                alert('Erro ao sincronizar: ' + error.message);
            }
        }
        
        function atualizarInterface() {
            let total = 0;
            let html = '';[index.html](https://github.com/user-attachments/files/22504211/index.html)

            
            gastos.forEach(g => {
                total += g.valor;
                html += `
                    <div style="padding: 10px; border-bottom: 1px solid #eee;">
                        <strong>${g.descricao}</strong><br>
                        <small>${g.data} • ${g.categoria}</small><br>
                        R$ ${g.valor.toFixed(2)}
                    </div>
                `;
            });
            
            document.getElementById('total').textContent = total.toFixed(2);
            document.getElementById('contador').textContent = gastos.length;
            document.getElementById('lista').innerHTML = html || '<p>Nenhum gasto</p>';
        }
        
        function atualizarStatus(mensagem, tipo) {
            document.getElementById('status').textContent = mensagem;
            document.getElementById('status').className = `status ${tipo}`;
        }
    </script>
</body>
</html>
