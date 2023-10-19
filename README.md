# fluxfinanc
Fluxo Financeiro

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Gastos Financeiros</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"
        integrity="sha384-KyZXEAg3QhqLMpG8r+JnU02yyVzXyTk5Kr/eRd3jUg1z4gk5x2Jk5a7b0C4Tb7I3g" crossorigin="anonymous">
        <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.4/xlsx.full.min.js"></script>

    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f2f2f2;
            margin: 0;
            padding: 0;
        }

        #container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #fff;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            border-radius: 5px;
        }

        h1 {
            text-align: center;
        }

        .data-hora {
            text-align: right;
            font-weight: bold;
        }

        #form {
            text-align: center;
            margin-top: 20px;
        }

        #form label {
            display: block;
            font-weight: bold;
        }

        #form input[type="text"],
        #form input[type="number"] {
            width: 100%;
            padding: 8px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }

        #form button {
            background-color: #007bff;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
        }

        #form button:hover {
            background-color: #0056b3;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }

        table th,
        table td {
            border: 1px solid #ccc;
            padding: 10px;
            text-align: left;
        }

        table th {
            background-color: #f2f2f2;
        }

        #total {
            text-align: right;
            font-weight: bold;
            margin-top: 20px;
        }

        button.export-button {
            background-color: #28a745;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
        }

        button.export-button:hover {
            background-color: #1e7e34;
        }

        .custom-select {
            width: 100%;
            padding: 8px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }

        button.remover-errado {
            background-color: #28a745;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
        }
    </style>
</head>

<body style="background-color: #97acca;">
    <div id="container" style="width: 1200px; height: 800px;">
        <h1>Fluxo Financeiro</h1>
        <div class="data-hora" id="data-hora-text"></div>
        <form id="form">
            <div>
                <label for="descricao">Produto:</label>
                <select id="descricao" required class="custom-select">
                    <option value="" disabled selected>Selecione um produto</option>
                </select>
            </div>
            <div>
                <label for="quantidade">Quantidade:</label>
                <input type="number" id="quantidade" required>
            </div>
            <div>
                <label for="valor">Preço Unitário:</label>
                <input type="number" id="valor" required>
            </div>
            <button type="button" onclick="adicionarDespesa()">Adicionar </button>
        </form>
        <table>
            <thead>
                <tr>
                    <th>Produto</th>
                    <th>Quantidade</th>
                    <th>Preço Unitário</th>
                    <th>Valor Total</th>
                    <th>Remover</th>
                </tr>
            </thead>
            <tbody id="tabelaDespesas">
            </tbody>
        </table>
        <p id="total">Total: R$ <span id="totalDespesas">0.00</span></p>
        <button class="export-button" onclick="exportarRelatorioParaTxt()">Exportar Relatorio</button>
    </div>

    <script>
        function atualizarDataHora() {
            const dataHoraElement = document.getElementById('data-hora-text');
            const agora = new Date();
            dataHoraElement.textContent = agora.toLocaleString();
        }

        setInterval(atualizarDataHora, 1000);
        atualizarDataHora();

            function exportarRelatorioParaTxt() {
            const tabela = document.getElementById('tabelaDespesas');
            const agora = new Date();
            const dataHora = agora.toLocaleString();

        let conteudo = `Relatório de Despesas - ${dataHora}\n\n`;

        // Percorra as linhas da tabela e exclua a última coluna
        for (let i = 0; i < tabela.rows.length; i++) {
            for (let j = 0; j < tabela.rows[i].cells.length - 1; j++) {
                conteudo += tabela.rows[i].cells[j].innerText + '\t';
            }
            conteudo += '\n';
        }

        // Otimizar e formatar o arquivo de texto
        conteudo = conteudo.replace(/\t/g, '\t\t'); // Adicionar um tab extra para formatação
        conteudo = conteudo.replace(/\n/g, '\n\n'); // Adicionar uma linha em branco para separar as linhas

        // Crie um blob do arquivo de texto
        const blob = new Blob([conteudo], { type: 'text/plain' });

        // Crie um link para fazer o download do arquivo de texto
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = 'relatorio_despesas.txt';
        a.click();
        }

        // Lista de produtos disponíveis
        const produtos = ["Cachorro Quente Simples",
            "Cachorro Quente Completo",
            "Caldo Feijão",
            "Caldo Madioca",
            "Salgado",
            "Refrigerante",
            "Bala",
            "Chocolate",
            "Chips",
            "Água",
            "Chicletes",
            "Pirulito",
            "Vitamina",
            "Café",
            "Leite",
            "Toddy",
            "Café com Leite",
            "Pastel",
            "Pipoca"];

        // Preencher o select com as opções de produtos
        const selectProduto = document.getElementById('descricao');
        for (let i = 0; i < produtos.length; i++) {
            const option = document.createElement('option');
            option.value = produtos[i];
            option.text = produtos[i];
            selectProduto.appendChild(option);
        }

        function adicionarDespesa() {
            const selectProduto = document.getElementById('descricao');
            const quantidade = parseInt(document.getElementById('quantidade').value);
            const valor = parseFloat(document.getElementById('valor').value);

            if (selectProduto.value && !isNaN(quantidade) && !isNaN(valor)) {
                const tabela = document.getElementById('tabelaDespesas');
                const newRow = tabela.insertRow(tabela.rows.length);

                const cell1 = newRow.insertCell(0);
                const cell2 = newRow.insertCell(1);
                const cell3 = newRow.insertCell(2);
                const cell4 = newRow.insertCell(3);

                cell1.innerHTML = selectProduto.value;
                cell2.innerHTML = quantidade;
                cell3.innerHTML = `R$ ${valor.toFixed(2)}`;
                cell4.innerHTML = `R$ ${(quantidade * valor).toFixed(2)}`;

                const totalDespesas = parseFloat(document.getElementById('totalDespesas').innerHTML);
                document.getElementById('totalDespesas').innerHTML = (totalDespesas + quantidade * valor).toFixed(2);

                // Limpar os campos após a adição
                selectProduto.value = "";
                document.getElementById('quantidade').value = '';
                document.getElementById('valor').value = '';

                // Adicione um botão de remoção à nova linha da tabela
                const cell5 = newRow.insertCell(4);
                const removeButton = document.createElement("button");
                removeButton.textContent = "Remover";
                removeButton.addEventListener("click", function () {
                    removerDespesa(newRow);
                });
                cell5.appendChild(removeButton);
            }
        }

        function removerDespesa(row) {
            const valorRemovido = parseFloat(row.cells[3].textContent.replace("R$ ", ""));
            const totalDespesas = parseFloat(document.getElementById('totalDespesas').innerHTML);
            document.getElementById('totalDespesas').innerHTML = (totalDespesas - valorRemovido).toFixed(2);

            // Remova a linha da tabela
            row.remove();
        }

        function mostrarCampoPersonalizado() {
            const selectQuantidade = document.getElementById('quantidade');
            const quantidadePersonalizada = document.getElementById('quantidadePersonalizada');

            if (selectQuantidade.value === 'outro') {
                quantidadePersonalizada.style.display = 'inline-block';
            } else {
                quantidadePersonalizada.style.display = 'none';
            }
        }


    </script>
</body>

</html>
