# Formul-rio-de-Ocorr-ncias
# Realizar e documentar falhas do S.O
<!DOCTYPE html>
<html>
<head>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(to bottom, #3498db, #f39c12);
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        .container {
            max-width: 800px;
            padding: 20px;
            background-color: #fff;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
        }

        h1 {
            text-align: center;
            color: #fff;
            background: linear-gradient(to right, #3498db, #f39c12);
            padding: 10px;
            border-radius: 8px;
        }

        label {
            display: block;
            margin-bottom: 10px;
        }

        input, textarea, select, button {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
            margin-bottom: 10px;
        }

        /* Centraliza os campos no centro do formulário */
        #nome, #matricula, #descricao {
            margin: 0 auto;
        }

        button {
            background-color: #f39c12;
            color: #fff;
            border: none;
            cursor: pointer;
            padding: 10px;
            border-radius: 4px;
        }

        button:hover {
            background-color: #e2900d;
        }

        .admin-buttons {
            text-align: center;
        }

        table {
            border-collapse: collapse;
            width: 100%;
        }

        table, th, td {
            border: 1px solid #ccc;
        }

        th, td {
            padding: 8px;
            text-align: center;
        }

        footer {
            text-align: center;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <header>
        <br><h1>Formulário de Ocorrências SAC</h1>
    </header>
    <div class="container">
        <form id="falhaForm">
            <label for="nome">Nome:</label>
            <input type="text" id="nome" name="nome" required>

            <label for="matricula">Matrícula:</label>
            <input type="text" id="matricula" name "matricula" required>

            <label for="motivo">Motivo da Falha:</label>
            <select id="motivo" name="motivo" required>
                <option value="troca_de_maquina">Troca de Máquina</option>
                <option value="falha_de_perifericos">Falha de Periféricos</option>
                <option value="queda_de_ligacao">Queda de Ligação</option>
                <option value="falha_sistemica">Falha Sistêmica</option>
                <option value="erro_de_rede">Erro de Rede</option>
                <option value="sem_acesso_a_ferramentas">Sem Acesso a Ferramentas(Top saúde,onbase,tasy)</option>
            </select>

            <label for="descricao">Descrição da Falha:</label>
            <textarea id="descricao" name="descricao" required></textarea>

            <button type="button" id="registrarButton">Registrar Falha</button>
            <button type="button" id="exibirFalhasButton">Exibir Falhas</button>

            <div class="admin-buttons">
                <input type="password" id="senha" placeholder="Senha do Super Usuário">
                <button type="button" id="exportarCSVButton">Exportar para CSV</button>
                <button type="button" id="limparHistoricoButton">Limpar Histórico</button>
            </div>
        </form>

        <div id="falhasRegistradas">
            <h2>Falhas Registradas:</h2>
            <table>
                <thead>
                    <tr>
                        <th>Data e Hora</th>
                        <th>Nome</th>
                        <th>Matrícula</th>
                        <th>Motivo da Falha</th>
                        <th>Descrição da Falha</th>
                    </tr>
                </thead>
                <tbody id="falhasList"></tbody>
            </table>
        </div>
    </div>
    <footer>
     <br>   <p>&copy; 2023 Matheus Vasconcelos, Analista de Desenvolvimento em Sistemas</p>
    </footer>
    
    <script>
        const falhaForm = document.getElementById('falhaForm');
        const falhasList = document.getElementById('falhasList');
        const registrarButton = document.getElementById('registrarButton');
        const exibirFalhasButton = document.getElementById('exibirFalhasButton');
        const exportarCSVButton = document.getElementById('exportarCSVButton');
        const limparHistoricoButton = document.getElementById('limparHistoricoButton');
        const senhaInput = document.getElementById('senha');

        const superUsuarioSenha = 'gndi';

        registrarButton.addEventListener('click', () => {
            const nome = document.getElementById('nome').value;
            const matricula = document.getElementById('matricula').value;
            const motivo = document.getElementById('motivo').value;
            const descricao = document.getElementById('descricao').value;
            const dataHora = new Date();

            if (nome && matricula && motivo && descricao) {
                const registrosAnteriores = JSON.parse(localStorage.getItem('registros')) || [];

                const novoRegistro = {
                    dataHora: dataHora,
                    nome: nome,
                    matricula: matricula,
                    motivo: motivo,
                    descricao: descricao
                };
                registrosAnteriores.push(novoRegistro);

                localStorage.setItem('registros', JSON.stringify(registrosAnteriores));

                document.getElementById('nome').value = '';
                document.getElementById('matricula').value = '';
                document.getElementById('motivo').value = '';
                document.getElementById('descricao').value = '';
            }
        });

        exibirFalhasButton.addEventListener('click', () => {
            falhasList.innerHTML = '';

            const registros = JSON.parse(localStorage.getItem('registros')) || [];

            registros.sort((a, b) => a.dataHora - b.dataHora);

            registros.forEach((registro) => {
                const dataHoraFormatada = new Intl.DateTimeFormat('pt-BR', {
                    dateStyle: 'short',
                    timeStyle: 'short'
                }).format(new Date(registro.dataHora));

                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${dataHoraFormatada}</td>
                    <td>${registro.nome}</td>
                    <td>${registro.matricula}</td>
                    <td>${registro.motivo}</td>
                    <td>${registro.descricao}</td>
                `;

                falhasList.appendChild(row);
            });
        });

        exportarCSVButton.addEventListener('click', () => {
            if (senhaInput.value === superUsuarioSenha) {
                const registros = JSON.parse(localStorage.getItem('registros')) || [];

                if (registros.length > 0) {
                    registros.sort((a, b) => a.dataHora - b.dataHora);

                    const csvData = [['Data e Hora', 'Nome', 'Matrícula', 'Motivo da Falha', 'Descrição da Falha']];
                    registros.forEach((registro) => {
                        const dataHoraFormatada = new Intl.DateTimeFormat('pt-BR', {
                            dateStyle: 'short',
                            timeStyle: 'short'
                        }).format(new Date(registro.dataHora));

                        csvData.push([dataHoraFormatada, registro.nome, registro.matricula, registro.motivo, registro.descricao]);
                    });

                    const csvContent = "data:text/csv;charset=utf-8," + csvData.map(e => e.join(",")).join("\n");

                    const encodedUri = encodeURI(csvContent);
                    const link = document.createElement("a");
                    link.setAttribute("href", encodedUri);
                    link.setAttribute("download", "relatorio.csv");
                    document.body.appendChild(link);
                    link.click();
                }
            } else {
                alert('Senha do Super Usuário incorreta. Você não tem permissão para exportar.');
            }
        });

        limparHistoricoButton.addEventListener('click', () => {
            if (senhaInput.value === superUsuarioSenha) {
                localStorage.removeItem('registros');
                falhasList.innerHTML = '';
            } else {
                alert('Senha do Super Usuário incorreta. Você não tem permissão para limpar o histórico.');
            }
        });
    </script>
</body>
</html>

