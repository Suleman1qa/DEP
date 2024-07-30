import React, { useState, useEffect } from 'react';
import { View, FlatList, Button, Text, TouchableOpacity, TextInput } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import DateTimePicker from '@react-native-community/datetimepicker';
import { Picker } from '@react-native-picker/picker';

const Stack = createStackNavigator();

const HomeScreen = ({ navigation }) => {
  const [tasks, setTasks] = useState([]);

  useEffect(() => {
    const fetchTasks = async () => {
      const storedTasks = await AsyncStorage.getItem('tasks');
      if (storedTasks) setTasks(JSON.parse(storedTasks));
    };
    fetchTasks();
  }, []);

  const addTask = () => {
    navigation.navigate('Task');
  };

  const editTask = (task) => {
    navigation.navigate('Task', { task });
  };

  const deleteTask = async (id) => {
    const updatedTasks = tasks.filter((task) => task.id !== id);
    setTasks(updatedTasks);
    await AsyncStorage.setItem('tasks', JSON.stringify(updatedTasks));
  };

  return (
    <View>
      <FlatList
        data={tasks}
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View>
            <Text>{item.title}</Text>
            <Text>{item.category}</Text>
            <Text>{new Date(item.dueDate).toLocaleDateString()}</Text>
            <Button title="Edit" onPress={() => editTask(item)} />
            <Button title="Delete" onPress={() => deleteTask(item.id)} />
          </View>
        )}
      />
      <Button title="Add Task" onPress={addTask} />
    </View>
  );
};

const TaskScreen = ({ route, navigation }) => {
  const { task } = route.params || {};
  const [title, setTitle] = useState(task ? task.title : '');
  const [category, setCategory] = useState(task ? task.category : '');
  const [dueDate, setDueDate] = useState(task ? new Date(task.dueDate) : new Date());

  useEffect(() => {
    if (task) {
      setTitle(task.title);
      setCategory(task.category);
      setDueDate(new Date(task.dueDate));
    }
  }, [task]);

  const saveTask = async () => {
    const newTask = { id: task ? task.id : Date.now(), title, category, dueDate: dueDate.toISOString() };
    const storedTasks = await AsyncStorage.getItem('tasks');
    const tasks = storedTasks ? JSON.parse(storedTasks) : [];
    if (task) {
      const index = tasks.findIndex((t) => t.id === task.id);
      tasks[index] = newTask;
    } else {
      tasks.push(newTask);
    }
    await AsyncStorage.setItem('tasks', JSON.stringify(tasks));
    navigation.goBack();
  };

  return (
    <View>
      <TextInput placeholder="Task Title" value={title} onChangeText={setTitle} />
      <Picker selectedValue={category} onValueChange={(itemValue) => setCategory(itemValue)}>
        <Picker.Item label="Personal" value="personal" />
        <Picker.Item label="Work" value="work" />
      </Picker>
      <DateTimePicker value={dueDate} mode="date" display="default" onChange={(event, selectedDate) => setDueDate(selectedDate || dueDate)} />
      <Button title="Save Task" onPress={saveTask} />
    </View>
  );
};

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Task" component={TaskScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default App;
