# API-Flask-inventario
API REST con Flask para CRUD básico de inventario. Endpoints GET/POST/PUT


from flask import Flask, request, jsonify
import sqlite3

app = Flask(__name__)
DB = 'inventario.db'

def init_db():
    conn = sqlite3.connect(DB)
    conn.execute('CREATE TABLE IF NOT EXISTS productos (id INTEGER PRIMARY KEY, nombre TEXT, stock INTEGER, precio REAL)')
    conn.commit()
    conn.close()

@app.route('/productos', methods=['GET'])
def get_productos():
    conn = sqlite3.connect(DB)
    productos = conn.execute('SELECT * FROM productos').fetchall()
    conn.close()
    return jsonify([dict(zip(['id','nombre','stock','precio'], p)) for p in productos])

@app.route('/productos', methods=['POST'])
def add_producto():
    data = request.json
    conn = sqlite3.connect(DB)
    conn.execute('INSERT INTO productos (nombre, stock, precio) VALUES (?,?,?)',
                 (data['nombre'], data['stock'], data['precio']))
    conn.commit()
    conn.close()
    return jsonify({'status': 'ok'}), 201

@app.route('/productos/<int:id>', methods=['PUT'])
def update_producto(id):
    data = request.json
    conn = sqlite3.connect(DB)
    conn.execute('UPDATE productos SET stock=? WHERE id=?', (data['stock'], id))
    conn.commit()
    conn.close()
    return jsonify({'status': 'actualizado'})

if __name__ == '__main__':
    init_db()
    app.run(debug=True)

#detalles de uso


1. `pip install flask`
2. `python api_inventario.py`
3. GET: `curl http://localhost:5000/productos`
4. POST: `curl -X POST -H "Content-Type: application/json" -d '{"nombre":"Leche","stock":20,"precio":150}' http://localhost:5000/productos`
5. PUT: `curl -X PUT -H "Content-Type: application/json" -d '{"stock":15}' http://localhost:5000/productos/1`

