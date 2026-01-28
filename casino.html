from flask import Flask, request
from flask_socketio import SocketIO, join_room, emit
import random
import string
import itertools

app = Flask(__name__)
app.config['SECRET_KEY'] = 'casino_perron_secret'
socketio = SocketIO(app, cors_allowed_origins="*")

rooms = {}

# --- UTILIDADES DE CARTAS ---
SUITS = ['♠', '♥', '♦', '♣']
RANKS = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A']
RANK_VALUES = {r: i for i, r in enumerate(RANKS, 2)}
RANK_NAMES = {
    2: 'Doses', 3: 'Treses', 4: 'Cuatros', 5: 'Cincos', 6: 'Seises',
    7: 'Sietes', 8: 'Ochos', 9: 'Nueves', 10: 'Dieces',
    11: 'Jotas', 12: 'Reinas', 13: 'Reyes', 14: 'Ases'
}
RANK_NAMES_SINGULAR = {
    2: 'Dos', 3: 'Tres', 4: 'Cuatro', 5: 'Cinco', 6: 'Seis',
    7: 'Siete', 8: 'Ocho', 9: 'Nueve', 10: 'Diez',
    11: 'Jota', 12: 'Reina', 13: 'Rey', 14: 'As'
}

def create_deck():
    deck = [{'rank': r, 'suit': s} for s in SUITS for r in RANKS]
    random.shuffle(deck)
    return deck

# --- LÓGICA DE BLACKJACK ---
def calculate_blackjack_score(hand):
    score = 0
    aces = 0
    for card in hand:
        r = card['rank']
        if r in ['J', 'Q', 'K']:
            score += 10
        elif r == 'A':
            aces += 1
            score += 11
        else:
            score += int(r)
    while score > 21 and aces > 0:
        score -= 10
        aces -= 1
    return score

class BlackjackGame:
    def __init__(self):
        self.deck = create_deck()
        self.hands = {}
        self.scores = {}
        self.turn_index = 0
        self.players_in_game = []
        self.status = 'waiting'
        self.winners = []
        self.win_reason = "" 
        self.config = {}
        self.pot = 0
        self.bet = 0

    def start_game(self, players, config):
        self.players_in_game = players
        self.config = config
        self.bet = int(config.get('bet', 0))
        self.pot = self.bet * len(players)
        
        self.deck = create_deck()
        self.hands = {p: [] for p in players}
        self.scores = {p: 0 for p in players}
        self.status = 'playing'
        self.turn_index = 0
        self.winners = []
        self.win_reason = ""
        
        for _ in range(2):
            for p in players:
                if self.deck: self.hands[p].append(self.deck.pop())
        self.update_scores()

    def update_scores(self):
        for p in self.players_in_game:
            self.scores[p] = calculate_blackjack_score(self.hands[p])

    def player_action(self, player, action, data=None):
        if self.status != 'playing': return
        current_p = self.players_in_game[self.turn_index]
        if player != current_p: return

        if action == 'hit':
            if self.deck:
                self.hands[player].append(self.deck.pop())
                self.update_scores()
                if self.scores[player] > 21:
                    self.next_turn()
        elif action == 'stand':
            self.next_turn()
        
        if self.status == 'finished':
            return 'game_over'
        return 'update'

    def next_turn(self):
        self.turn_index = (self.turn_index + 1) % len(self.players_in_game)
        if self.turn_index == 0: 
            self.end_round()

    def end_round(self):
        active = {p: s for p, s in self.scores.items() if s <= 21}
        if not active:
            self.status = 'finished'
            self.winners = []
            self.win_reason = "Todos se pasaron"
            return

        quintillas = [p for p in active if len(self.hands[p]) >= 5]
        if quintillas:
            candidates = quintillas
            self.win_reason = "Quintilla (5 cartas)"
        else:
            max_s = max(active.values())
            candidates = [p for p, s in active.items() if s == max_s]
            self.win_reason = f"Puntaje Alto ({max_s})"

        if len(candidates) > 1:
            self.win_reason += " + Carta Alta"
            self.winners = [candidates[0]] 
        else:
            self.winners = candidates
        self.status = 'finished'

    def get_state(self):
        turn_name = self.players_in_game[self.turn_index] if self.status == 'playing' and self.players_in_game else None
        return {
            'status': self.status,
            'turn': turn_name,
            'hands': self.hands,
            'scores': self.scores,
            'winners': self.winners,
            'win_reason': self.win_reason,
            'game_type': 'blackjack',
            'pot': self.pot,
            'bet': self.bet
        }

# --- LÓGICA DE POKER GENÉRICA ---

def get_rank_name(val, plural=True):
    return RANK_NAMES.get(val, str(val)) if plural else RANK_NAMES_SINGULAR.get(val, str(val))

def _eval_standard(cards):
    values = sorted([RANK_VALUES[c['rank']] for c in cards], reverse=True)
    suits = [c['suit'] for c in cards]
    rank_counts = {r: values.count(r) for r in values}
    sorted_counts = sorted(rank_counts.items(), key=lambda item: (item[1], item[0]), reverse=True)
    
    is_flush = len(set(suits)) == 1
    
    unique_vals = sorted(list(set(values)))
    is_straight = False
    straight_high = 0
    
    if len(unique_vals) >= 5:
        for i in range(len(unique_vals) - 4):
            if unique_vals[i+4] - unique_vals[i] == 4:
                is_straight = True
                straight_high = unique_vals[i+4]
                break
        if not is_straight and set([14, 2, 3, 4, 5]).issubset(set(values)):
            is_straight = True
            straight_high = 5

    hand_name = ""
    score = 0
    tie_breakers = [r for r, count in sorted_counts]

    primary_rank = sorted_counts[0][0]
    secondary_rank = sorted_counts[1][0] if len(sorted_counts) > 1 else 0

    if is_straight and is_flush:
        if 14 in values and 13 in values: 
            hand_name = "Escalera Real"
            score = 9
        else: 
            hand_name = f"Escalera de Color al {get_rank_name(straight_high, False)}"
            score = 8
    elif sorted_counts[0][1] == 4:
        hand_name = f"Poker de {get_rank_name(primary_rank)}"
        score = 7
    elif sorted_counts[0][1] == 3 and sorted_counts[1][1] == 2:
        hand_name = f"Full House ({get_rank_name(primary_rank)} con {get_rank_name(secondary_rank)})"
        score = 6
    elif is_flush:
        hand_name = f"Color al {get_rank_name(values[0], False)}"
        score = 5
    elif is_straight:
        hand_name = f"Escalera al {get_rank_name(straight_high, False)}"
        score = 4
    elif sorted_counts[0][1] == 3:
        hand_name = f"Tercia de {get_rank_name(primary_rank)}"
        score = 3
    elif sorted_counts[0][1] == 2 and sorted_counts[1][1] == 2:
        hand_name = f"Doble Par ({get_rank_name(primary_rank)} y {get_rank_name(secondary_rank)})"
        score = 2
    elif sorted_counts[0][1] == 2:
        hand_name = f"Par de {get_rank_name(primary_rank)}"
        score = 1
    else:
        hand_name = f"Carta Alta ({get_rank_name(values[0], False)})"
        score = 0
        
    return (score, tie_breakers, hand_name)

def eval_poker_hand_5(cards, wild_rank=None):
    normal_cards = []
    wild_count = 0
    
    for c in cards:
        if wild_rank and c['rank'] == wild_rank:
            wild_count += 1
        else:
            normal_cards.append(c)

    if wild_count == 0:
        return _eval_standard(cards)
    
    full_deck = [{'rank': r, 'suit': s} for s in SUITS for r in RANKS]
    available = [c for c in full_deck if not any(nc['rank'] == c['rank'] and nc['suit'] == c['suit'] for nc in normal_cards)]
    
    best_hand_val = (-1, [], "")
    
    # Solo probar combinaciones necesarias
    import itertools
    # Limitar iteraciones si hay demasiados comodines para evitar lag, 
    # aunque con 5 cartas max 5 wildcards es rapido.
    for replacement in itertools.combinations(available, wild_count):
        test_hand = normal_cards + list(replacement)
        val = _eval_standard(test_hand)
        if val > best_hand_val:
            best_hand_val = val
            
    return (best_hand_val[0], best_hand_val[1], best_hand_val[2] + " (con Comodín)")

def eval_texas_hand(hole_cards, community_cards):
    """
    Evalúa la mejor mano de 5 cartas de las 7 disponibles (2 hole + 5 community).
    """
    all_cards = hole_cards + community_cards
    # Generar todas las combinaciones de 5 cartas
    best_score = (-1, [], "")
    
    # Si hay menos de 5 cartas (no debería pasar al final), evaluar lo que hay
    if len(all_cards) < 5:
        return _eval_standard(all_cards)

    for combo in itertools.combinations(all_cards, 5):
        score = _eval_standard(list(combo))
        if score > best_score:
            best_score = score
            
    return best_score


class PokerGame:
    def __init__(self):
        self.deck = []
        self.hands = {} 
        self.status = 'waiting'
        self.players_in_game = []
        self.players_ready = [] 
        self.winners = []
        self.win_reason = ""
        self.hand_names = {} 
        self.config = {}
        self.wild_rank = None
        self.pot = 0
        self.bet = 0

    def start_game(self, players, config):
        self.players_in_game = players
        self.config = config
        self.bet = int(config.get('bet', 0))
        self.pot = self.bet * len(players)
        
        self.deck = create_deck()
        self.hands = {p: [] for p in players}
        self.players_ready = []
        self.status = 'discard_phase'
        self.winners = []
        self.win_reason = ""
        self.hand_names = {p: "?" for p in players}
        
        self.wild_rank = None
        if config.get('wildcards', False):
            self.wild_rank = random.choice(RANKS)
        
        for _ in range(5):
            for p in players:
                if self.deck: self.hands[p].append(self.deck.pop())

    def player_action(self, player, action, data=None):
        if self.status != 'discard_phase': return
        
        if action == 'exchange':
            if player in self.players_ready: return
            
            indices = sorted(data.get('discard_indices', []), reverse=True)
            current_hand = self.hands[player]
            
            for idx in indices:
                if 0 <= idx < len(current_hand):
                    current_hand.pop(idx)
            
            while len(current_hand) < 5 and self.deck:
                current_hand.append(self.deck.pop())
                
            self.hands[player] = current_hand
            self.players_ready.append(player)
            
            if len(self.players_ready) >= len(self.players_in_game):
                self.evaluate_winner()
                return 'game_over'
            
            return 'update'

    def evaluate_winner(self):
        best_score = (-1, [], "")
        winners = []
        
        for p in self.players_in_game:
            score_tuple = eval_poker_hand_5(self.hands[p], self.wild_rank)
            self.hand_names[p] = score_tuple[2]
            
            if score_tuple > best_score:
                best_score = score_tuple
                winners = [p]
            elif score_tuple == best_score:
                winners.append(p)
        
        self.winners = winners
        self.win_reason = best_score[2]
        self.status = 'finished'

    def get_state(self):
        return {
            'status': self.status,
            'hands': self.hands,
            'ready_players': self.players_ready,
            'winners': self.winners,
            'win_reason': self.win_reason,
            'hand_names': self.hand_names,
            'game_type': 'poker',
            'wild_rank': self.wild_rank,
            'pot': self.pot,
            'bet': self.bet
        }

class TexasHoldemGame:
    def __init__(self):
        self.deck = []
        self.hands = {} # Cartas propias (2)
        self.community_cards = []
        self.status = 'waiting' # betting_flop, betting_turn, betting_river, finished
        self.players_in_game = []
        self.active_players = [] # Jugadores que no se han retirado
        self.decisions = {} # {player: 'call'/'fold'}
        self.winners = []
        self.win_reason = ""
        self.hand_names = {}
        self.pot = 0
        self.initial_bet = 0
        self.bet = 0

    def start_game(self, players, config):
        self.players_in_game = players
        self.active_players = list(players)
        self.initial_bet = int(config.get('bet', 0))
        self.bet = self.initial_bet # Apuesta de la ronda actual
        self.pot = self.initial_bet * len(players) # Ante inicial
        
        self.deck = create_deck()
        self.hands = {p: [] for p in players}
        self.community_cards = []
        self.decisions = {}
        self.winners = []
        self.hand_names = {p: "?" for p in players}
        
        # Repartir 2 cartas a cada jugador
        for _ in range(2):
            for p in players:
                if self.deck: self.hands[p].append(self.deck.pop())
        
        # Flop inicial (3 cartas) - Regla especifica del usuario
        for _ in range(3):
            if self.deck: self.community_cards.append(self.deck.pop())
            
        self.status = 'betting_flop' # Primera ronda de apuestas

    def player_action(self, player, action, data=None):
        # action: 'call' (Ir) o 'fold' (No Ir)
        if player not in self.active_players: return
        if player in self.decisions: return # Ya decidió en esta ronda
        
        self.decisions[player] = action
        
        if action == 'call':
            self.pot += self.initial_bet # Apuesta adicional
        elif action == 'fold':
            self.active_players.remove(player)
            # Si se retira, no paga más, pero pierde lo anterior
            
        # Checar si todos los activos han decidido
        if len(self.decisions) >= len(self.active_players) + (len(self.players_in_game) - len(self.active_players)): 
             # Nota: self.decisions acumula todos, incluso los que se retiraron si lo guardamos,
             # pero aqui limpiamos decisions por ronda.
             # Mejor logica: contar decisiones de los activos actuales.
             decided_active = [p for p in self.active_players if p in self.decisions]
             if len(decided_active) == len(self.active_players):
                 self.advance_stage()
                 if self.status == 'finished':
                     return 'game_over'
                 return 'update'
                 
        # Victoria por abandono (solo queda 1)
        if len(self.active_players) == 1:
            self.end_game_by_fold()
            return 'game_over'

        return 'update'

    def advance_stage(self):
        # Resetear decisiones para la siguiente ronda
        self.decisions = {}
        
        if self.status == 'betting_flop':
            # Revelar Turn (4ta carta)
            if self.deck: self.community_cards.append(self.deck.pop())
            self.status = 'betting_turn'
            
        elif self.status == 'betting_turn':
            # Revelar River (5ta carta)
            if self.deck: self.community_cards.append(self.deck.pop())
            self.status = 'showdown' # Prompt dice: ver 5ta carta y gana el mejor
            self.evaluate_winner()
            
        elif self.status == 'showdown':
            # Ya se evaluó
            pass

    def evaluate_winner(self):
        best_score = (-1, [], "")
        winners = []
        
        for p in self.active_players:
            # Evaluar combinando mano + comunitarias
            score_tuple = eval_texas_hand(self.hands[p], self.community_cards)
            self.hand_names[p] = score_tuple[2]
            
            if score_tuple > best_score:
                best_score = score_tuple
                winners = [p]
            elif score_tuple == best_score:
                winners.append(p)
        
        self.winners = winners
        self.win_reason = best_score[2]
        self.status = 'finished'

    def end_game_by_fold(self):
        self.winners = [self.active_players[0]]
        self.win_reason = "Los demás se retiraron"
        self.status = 'finished'

    def get_state(self):
        return {
            'status': self.status,
            'hands': self.hands,
            'community_cards': self.community_cards,
            'active_players': self.active_players,
            'decided_players': list(self.decisions.keys()),
            'winners': self.winners,
            'win_reason': self.win_reason,
            'hand_names': self.hand_names,
            'game_type': 'texas',
            'pot': self.pot,
            'bet': self.initial_bet
        }

# --- GESTOR GLOBAL DE SALAS ---

@app.route('/')
def index():
    return "Servidor Casino Perrón Activo"

@socketio.on('create_room')
def on_create(data):
    username = data.get('host')
    game_type = data.get('game', 'blackjack') 
    wildcards = data.get('wildcards', False)
    bet = data.get('bet', 0)
    
    room_code = ''.join(random.choices(string.ascii_uppercase, k=4))
    
    game_instance = None
    if game_type == 'poker':
        game_instance = PokerGame()
    elif game_type == 'texas':
        game_instance = TexasHoldemGame()
    else:
        game_instance = BlackjackGame()

    rooms[room_code] = {
        'host': username,
        'players': [username],
        'sockets': {username: request.sid},
        'game_instance': game_instance,
        'game_type': game_type,
        'config': {'wildcards': wildcards, 'bet': bet}
    }
    
    join_room(room_code)
    emit('room_created', {'room_id': room_code, 'host': username, 'game_type': game_type}, room=request.sid)
    emit('update_players', {'players': [username], 'host': username}, room=room_code)

@socketio.on('join_room')
def on_join(data):
    room_code = data.get('room')
    username = data.get('player')
    
    if room_code not in rooms:
        emit('error', 'Sala no encontrada', room=request.sid)
        return
    
    room = rooms[room_code]
    if username in room['players']:
        emit('error', 'Nombre en uso', room=request.sid)
        return

    room['players'].append(username)
    room['sockets'][username] = request.sid
    join_room(room_code)
    
    emit('joined_room', {'room_id': room_code, 'host': room['host'], 'game_type': room['game_type']}, room=request.sid)
    emit('update_players', {'players': room['players'], 'host': room['host']}, room=room_code)

@socketio.on('start_game')
def on_start(data):
    room_code = data.get('room')
    if room_code in rooms:
        room = rooms[room_code]
        room['game_instance'].start_game(room['players'], room['config'])
        emit('game_started', room['game_instance'].get_state(), room=room_code)

@socketio.on('player_action')
def on_action(data):
    room_code = data.get('room')
    action = data.get('action')
    player = data.get('player')
    extra_data = data.get('data', {})
    
    if room_code in rooms:
        room = rooms[room_code]
        game = room['game_instance']
        result = game.player_action(player, action, extra_data)
        state = game.get_state()
        if result == 'game_over':
            emit('game_over', state, room=room_code)
        elif result == 'update':
            emit('update_game_state', state, room=room_code)

@socketio.on('send_message')
def on_send_message(data):
    room = data.get('room')
    player = data.get('player')
    message = data.get('message')
    if room in rooms:
        emit('chat_message', {'player': player, 'message': message}, room=room)

if __name__ == '__main__':
    socketio.run(app, debug=True, port=5000)
