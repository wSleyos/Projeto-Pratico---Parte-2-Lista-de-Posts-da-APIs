import React, { useState, useEffect } from 'react';
import {
  View,
  Text,
  StyleSheet,
  ActivityIndicator,
  FlatList,
  Pressable,
} from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

// TELA LISTA
function TelaLista({ navigation }) {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then((response) => response.json())
      .then((data) => {
        setUsers(data);
        setLoading(false);
      })
      .catch((error) => {
        console.error('Erro ao buscar usuários:', error);
        setLoading(false);
      });
  }, []);

  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" />
        <Text style={styles.loadingText}>Carregando usuários...</Text>
      </View>
    );
  }

  return (
    <FlatList
      data={users}
      keyExtractor={(item) => item.id.toString()}
      contentContainerStyle={styles.lista}
      renderItem={({ item }) => (
        <Pressable
          style={styles.card}
          onPress={() => navigation.navigate('TelaPerfil', { id: item.id })}
        >
          <Text style={styles.nome}>{item.name}</Text>
          <Text style={styles.email}>{item.email}</Text>
        </Pressable>
      )}
    />
  );
}

// TELA PERFIL
function TelaPerfil({ route }) {
  const { id } = route.params;
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/users/${id}`)
      .then((response) => response.json())
      .then((data) => {
        setUser(data);
        setLoading(false);
      })
      .catch((error) => {
        console.error('Erro ao buscar perfil:', error);
        setLoading(false);
      });
  }, [id]);

  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" />
        <Text style={styles.loadingText}>Carregando perfil...</Text>
      </View>
    );
  }

  return (
    <View style={styles.perfilContainer}>
      <Text style={styles.titulo}>Perfil do Usuário 👤</Text>
      <Text style={styles.info}>Nome: {user.name}</Text>
      <Text style={styles.info}>Email: {user.email}</Text>
      <Text style={styles.info}>Telefone: {user.phone}</Text>
      <Text style={styles.info}>Website: {user.website}</Text>
    </View>
  );
}

// APP PRINCIPAL
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen
          name="TelaLista"
          component={TelaLista}
          options={{ title: 'Lista de Usuários' }}
        />
        <Stack.Screen
          name="TelaPerfil"
          component={TelaPerfil}
          options={{ title: 'Perfil do Usuário' }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// ESTILOS
const styles = StyleSheet.create({
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  loadingText: {
    marginTop: 15,
    fontSize: 18,
    color: '#444',
  },
  lista: {
    padding: 15,
    backgroundColor: '#f2f2f2',
  },
  card: {
    backgroundColor: '#fff',
    padding: 15,
    marginBottom: 15,
    borderRadius: 12,
    elevation: 3,
  },
  nome: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#222',
    marginBottom: 5,
  },
  email: {
    fontSize: 16,
    color: '#666',
  },
  perfilContainer: {
    flex: 1,
    backgroundColor: '#f2f2f2',
    padding: 20,
    justifyContent: 'center',
  },
  titulo: {
    fontSize: 28,
    fontWeight: 'bold',
    marginBottom: 25,
    textAlign: 'center',
    color: '#111',
  },
  info: {
    fontSize: 20,
    marginBottom: 15,
    color: '#333',
  },
});
