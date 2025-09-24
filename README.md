<!DOCTYPE html>
<html>
<head>
    <title>💰 Meus Gastos - Teste</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body { font-family: Arial; padding: 20px; background: #f0f0f0; }
        .card { background: white; padding: 15px; margin: 10px 0; border-radius: 10px; }
        input, button { width: 100%; padding: 12px; margin: 5px 0; }
        button { background: blue; color: white; border: none; border-radius: 5px; }
    </style>
</head>
<body>
    <div class="card">
        <h2>💰 Meus Gastos - TESTE</h2>
        <p>Se você está vendo isso, o GitHub Pages está funcionando!</p>
    </div>

    <div class="card">
        <h3>➕ Adicionar Gasto</h3>
        <input type="text" id="descricao" placeholder="O que comprei?">
        <input type="number" id="valor" placeholder="Valor R$">
        <button onclick="adicionarGasto()">Adicionar Gasto</button>
    </div>

    <div class="card">
        <h3>📝 Meus Gastos</h3>
        <div id="lista"></div>
    </div>

    <script>
        let gastos = [];
        
        function adicionarGasto() {
            const descricao = document.getElementById('descricao').value;
            const valor = document.getElementById('valor').value;
            
            if (descricao && valor) {
                gastos.push({descricao, valor});
                atualizarLista();
                document.getElementById('descricao').value = '';
                document.getElementById('valor').value = '';
                alert('Gasto adicionado: ' + descricao);
            }
        }
        
        function atualizarLista() {
            let html = '';
            gastos.forEach(gasto => {
                html += `<p>${gasto.descricao} - R$ ${gasto.valor}</p>`;
            });
            document.getElementById('lista').innerHTML = html;
        }
    </script>
</body>
</html>
