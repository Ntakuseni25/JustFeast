import React, { useState, useEffect } from 'react';
import {
  View,
  Text,
  TextInput,
  Button,
  FlatList,
  StyleSheet,
  Image,
  TouchableOpacity,
  ScrollView,
  SafeAreaView,
} from 'react-native';
import * as ImagePicker from 'expo-image-picker';
import { Picker } from '@react-native-picker/picker';

export default function App() {
  const [menu, setMenu] = useState([]);
  const [orders, setOrders] = useState([]);
  const [name, setName] = useState('');
  const [description, setDescription] = useState('');
  const [price, setPrice] = useState('');
  const [course, setCourse] = useState('Starter');
  const [image, setImage] = useState(null);
  const [mealOfDay, setMealOfDay] = useState(null);

  useEffect(() => {
    if (menu.length > 0) {
      const randomDish = menu[Math.floor(Math.random() * menu.length)];
      setMealOfDay(randomDish);
    } else {
      setMealOfDay(null);
    }
  }, [menu]);

  const pickImage = async () => {
    const result = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      quality: 1,
    });

    if (!result.canceled) {
      setImage(result.assets[0].uri);
    }
  };

  const addDish = () => {
    if (!name || !description || !price) {
      alert('Please fill all fields');
      return;
    }

    const newDish = {
      id: Date.now().toString(),
      name,
      description,
      price,
      course,
      image,
    };

    setMenu([...menu, newDish]);
    setName('');
    setDescription('');
    setPrice('');
    setCourse('Starter');
    setImage(null);
  };

  const removeDish = (id) => {
    const updatedMenu = menu.filter((item) => item.id !== id);
    setMenu(updatedMenu);
  };

  const placeOrder = (dish) => {
    setOrders([...orders, { ...dish, orderId: Date.now().toString() }]);
    alert(`${dish.name} has been ordered!`);
  };

  return (
    <SafeAreaView style={styles.safeArea}>
      <ScrollView contentContainerStyle={styles.container}>
        <Text style={styles.title}>🍽️ Just Feast</Text>

        {/* Meal of the Day */}
        <Text style={styles.sectionTitle}>Meal of the Day</Text>
        {mealOfDay ? (
          <View style={styles.card}>
            {mealOfDay.image && (
              <Image source={{ uri: mealOfDay.image }} style={styles.image} />
            )}
            <Text style={styles.dishName}>
              {mealOfDay.name} ({mealOfDay.course})
            </Text>
            <Text>{mealOfDay.description}</Text>
            <Text>R{mealOfDay.price}</Text>
          </View>
        ) : (
          <Text>No dishes available.</Text>
        )}

        {/* Manager Section */}
        <Text style={styles.sectionTitle}>Manager - Add/Remove Dishes</Text>

        <TextInput
          style={styles.input}
          placeholder="Dish Name"
          value={name}
          onChangeText={setName}
        />
        <TextInput
          style={styles.input}
          placeholder="Description"
          value={description}
          onChangeText={setDescription}
        />
        <TextInput
          style={styles.input}
          placeholder="Price"
          value={price}
          onChangeText={setPrice}
          keyboardType="decimal-pad"
        />
        <Text>Select Course:</Text>
        <Picker
          selectedValue={course}
          onValueChange={(itemValue) => setCourse(itemValue)}
          style={styles.picker}
        >
          <Picker.Item label="Starter" value="Starter" />
          <Picker.Item label="Main" value="Main" />
          <Picker.Item label="Dessert" value="Dessert" />
        </Picker>
        <Button title="Pick Image" onPress={pickImage} />
        {image && <Image source={{ uri: image }} style={styles.imagePreview} />}
        <Button title="Add Dish" onPress={addDish} />

        {/* Menu */}
        <Text style={styles.sectionTitle}>📋 Full Menu</Text>
        {menu.length === 0 ? (
          <Text>No dishes added yet.</Text>
        ) : (
          <FlatList
            data={menu}
            keyExtractor={(item) => item.id}
            scrollEnabled={false}
            renderItem={({ item }) => (
              <View style={styles.card}>
                {item.image && (
                  <Image source={{ uri: item.image }} style={styles.image} />
                )}
                <Text style={styles.dishName}>
                  {item.name} ({item.course})
                </Text>
                <Text>{item.description}</Text>
                <Text>R{item.price}</Text>
                <View style={styles.cardButtons}>
                  <TouchableOpacity
                    onPress={() => removeDish(item.id)}
                    style={styles.removeBtn}
                  >
                    <Text style={styles.removeText}>Remove</Text>
                  </TouchableOpacity>
                  <TouchableOpacity
                    onPress={() => placeOrder(item)}
                    style={styles.orderBtn}
                  >
                    <Text style={styles.orderText}>Order</Text>
                  </TouchableOpacity>
                </View>
              </View>
            )}
          />
        )}

        {/* Orders */}
        <Text style={styles.sectionTitle}>🧾 Online Orders</Text>
        {orders.length === 0 ? (
          <Text>No orders placed yet.</Text>
        ) : (
          orders.map((order) => (
            <View key={order.orderId} style={styles.card}>
              <Text style={styles.dishName}>{order.name}</Text>
              <Text>Order ID: {order.orderId}</Text>
              <Text>R{order.price}</Text>
            </View>
          ))
        )}
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: { flex: 1, backgroundColor: '#fff' },
  container: { padding: 20, paddingBottom: 80 },
  title: {
    fontSize: 26,
    fontWeight: 'bold',
    marginBottom: 15,
    textAlign: 'center',
  },
  sectionTitle: {
    fontSize: 20,
    fontWeight: '600',
    marginTop: 25,
    marginBottom: 10,
  },
  input: {
    borderWidth: 1,
    borderColor: '#ccc',
    borderRadius: 6,
    padding: 10,
    marginBottom: 10,
  },
  picker: {
    marginBottom: 10,
    backgroundColor: '#f2f2f2',
  },
  imagePreview: {
    width: '100%',
    height: 150,
    borderRadius: 10,
    marginVertical: 10,
  },
  card: {
    backgroundColor: '#f9f9f9',
    padding: 12,
    borderRadius: 8,
    marginBottom: 15,
    elevation: 2,
  },
  cardButtons: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginTop: 10,
  },
  dishName: {
    fontWeight: 'bold',
    fontSize: 16,
    marginBottom: 5,
  },
  image: {
    width: '100%',
    height: 130,
    borderRadius: 8,
    marginBottom: 8,
  },
  removeBtn: {
    backgroundColor: '#e53935',
    padding: 8,
    borderRadius: 6,
    width: '48%',
    alignItems: 'center',
  },
  removeText: {
    color: '#fff',
    fontWeight: 'bold',
  },
  orderBtn: {
    backgroundColor: '#4caf50',
    padding: 8,
    borderRadius: 6,
    width: '48%',
    alignItems: 'center',
  },
  orderText: {
    color: '#fff',
    fontWeight: 'bold',
  },
});
