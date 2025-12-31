from flask import Flask, request
from flask_socketio import SocketIO, emit, join_room, leave_room
import random
import string
import time
import os

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret!'
# Permitir conexiones desde cualquier origen (CORS) para que funcione tu HTML
socketio = SocketIO(app, cors_allowed_origins="*", async_mode='eventlet')

# Base de datos en memoria (RAM)
# Estructura: { 'ROOM_ID': { status: 'waiting', players: { 'UID': { data... } } } }
games = {}

def generate_room_id():
    return ''.join(random.choices(string.digits, k=4))

@app.route('/')
def index():
    return "F1 Super Sex Server Running! 🏎️💨"

@socketio.on('create_game')
def on_create(data):
    player = data['player']
    uid = player['uid']
    room_id = generate_room_id()
    while room_id in games:
        room_id = generate_room_id()

    games[room_id] = {
        'roomId': room_id,
        'status': 'waiting',
        'hostId': uid,
        'players': {uid: player},
        'startTime': 0,
        'winner': None,
        'winnerName': None
    }
    join_room(room_id)
    emit('game_update', games[room_id], to=room_id)
    emit('room_created', {'roomId': room_id})
    print(f"Sala creada: {room_id}")

@socketio.on('join_game')
def on_join(data):
    room_id = data['roomId']
    player = data['player']
    uid = player['uid']

    if room_id not in games:
        emit('error_msg', {'msg': 'Sala no encontrada'})
        return

    game = games[room_id]
    
    if len(game['players']) >= 5:
        emit('error_msg', {'msg': 'Sala llena'})
        return

    # Si ya existe, actualizamos, si no, agregamos
    if uid not in game['players']:
        game['players'][uid] = player
    
    join_room(room_id)
    emit('game_update', game, to=room_id)
    print(f"Jugador {player['name']} unido a {room_id}")

@socketio.on('select_car')
def on_select_car(data):
    room_id = data['roomId']
    uid = data['uid']
    car_id = data['carId']
    
    if room_id in games and uid in games[room_id]['players']:
        # Verificar duplicados
        taken = False
        for p in games[room_id]['players'].values():
            if p['carId'] == car_id and p['uid'] != uid:
                taken = True
                break
        
        if not taken:
            games[room_id]['players'][uid]['carId'] = car_id
            emit('game_update', games[room_id], to=room_id)

@socketio.on('start_game')
def on_start(data):
    room_id = data['roomId']
    if room_id in games:
        # Asignar posiciones de parrilla
        players_list = list(games[room_id]['players'].values())
        total = len(players_list)
        for i, p in enumerate(players_list):
            if total > 1:
                p['x'] = -0.6 + (i * (1.2 / (total - 1)))
            else:
                p['x'] = 0
            games[room_id]['players'][p['uid']] = p

        games[room_id]['status'] = 'counting'
        games[room_id]['startTime'] = int(time.time() * 1000) + 3500
        emit('game_update', games[room_id], to=room_id)

@socketio.on('racing_start')
def on_racing_start(data):
    room_id = data['roomId']
    if room_id in games:
        games[room_id]['status'] = 'racing'
        emit('game_update', games[room_id], to=room_id)

@socketio.on('update_player')
def on_update(data):
    room_id = data['roomId']
    p_data = data['player']
    uid = p_data['uid']
    
    if room_id in games and uid in games[room_id]['players']:
        player = games[room_id]['players'][uid]
        # Actualizamos coordenadas
        player['x'] = p_data.get('x', player['x'])
        player['y'] = p_data.get('y', player['y'])
        player['finished'] = p_data.get('finished', player.get('finished', False))
        
        # Check Win
        if player['finished'] and not games[room_id]['winner']:
            games[room_id]['winner'] = uid
            games[room_id]['winnerName'] = player['name']
            
        # Check All Finished
        all_done = all(p.get('finished', False) for p in games[room_id]['players'].values())
        if all_done:
            games[room_id]['status'] = 'finished'
            
        # Rebotamos el estado a todos (Broadcast)
        # Nota: Para producción masiva, se suele usar un loop de servidor a X tickrate.
        # Para este prototipo, rebotar cada mensaje funciona bien hasta ~10 jugadores.
        emit('game_update', games[room_id], to=room_id)

@socketio.on('force_finish')
def on_force_finish(data):
    room_id = data['roomId']
    if room_id in games:
        games[room_id]['status'] = 'finished'
        emit('game_update', games[room_id], to=room_id)

if __name__ == '__main__':
    port = int(os.environ.get("PORT", 5000))
    socketio.run(app, host='0.0.0.0', port=port)
