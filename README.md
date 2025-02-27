// App.js - Main application entry point
import React, { useState, useEffect } from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { StatusBar } from 'react-native';

// Import screens
import LoginScreen from './screens/LoginScreen';
import RegisterScreen from './screens/RegisterScreen';
import ForgotPasswordScreen from './screens/ForgotPasswordScreen';
import CalendarScreen from './screens/CalendarScreen';
import MessageTypeScreen from './screens/MessageTypeScreen';
import TextMessageScreen from './screens/TextMessageScreen';
import AudioMessageScreen from './screens/AudioMessageScreen';
import VideoMessageScreen from './screens/VideoMessageScreen';
import GiftCatalogScreen from './screens/GiftCatalogScreen';
import CartScreen from './screens/CartScreen';
import PaymentScreen from './screens/PaymentScreen';
import ConfirmationScreen from './screens/ConfirmationScreen';
import AdminDashboardScreen from './screens/AdminDashboardScreen';
import AdminProductScreen from './screens/AdminProductScreen';

// Auth context
import { AuthProvider } from './context/AuthContext';
import { CartProvider } from './context/CartContext';

const Stack = createStackNavigator();

export default function App() {
  const [isLoading, setIsLoading] = useState(true);
  const [userToken, setUserToken] = useState(null);
  const [isAdmin, setIsAdmin] = useState(false);

  const checkUserSession = async () => {
    try {
      const token = await AsyncStorage.getItem('userToken');
      const userInfo = await AsyncStorage.getItem('userInfo');
      
   if (token && userInfo) {
        const user = JSON.parse(userInfo);
        setUserToken(token);
        setIsAdmin(user.email === 'dearfutureforyourlovedone@gmail.com');
      }
      
  setIsLoading(false);
    } catch (e) {
      console.log(e);
      setIsLoading(false);
    }
  };

  useEffect(() => {
    checkUserSession();
  }, []);

  if (isLoading) {
    // Show a splash screen or loading indicator
    return null;
  }

  return (
    <AuthProvider>
      <CartProvider>
        <NavigationContainer>
          <StatusBar backgroundColor="#FF8FAB" />
          <Stack.Navigator 
            screenOptions={{
              headerStyle: {
                backgroundColor: '#FF8FAB',
              },
              headerTintColor: '#fff',
              headerTitleStyle: {
                fontWeight: 'bold',
              },
            }}
          >
            {userToken === null ? (
              // Auth screens
              <>
                <Stack.Screen name="Login" component={LoginScreen} />
                <Stack.Screen name="Register" component={RegisterScreen} />
                <Stack.Screen name="ForgotPassword" component={ForgotPasswordScreen} />
              </>
            ) : isAdmin ? (
              // Admin screens
              <>
                <Stack.Screen name="AdminDashboard" component={AdminDashboardScreen} />
                <Stack.Screen name="AdminProduct" component={AdminProductScreen} />
              </>
            ) : (
              // User screens
              <>
                <Stack.Screen name="Calendar" component={CalendarScreen} />
                <Stack.Screen name="MessageType" component={MessageTypeScreen} />
                <Stack.Screen name="TextMessage" component={TextMessageScreen} />
                <Stack.Screen name="AudioMessage" component={AudioMessageScreen} />
                <Stack.Screen name="VideoMessage" component={VideoMessageScreen} />
                <Stack.Screen name="GiftCatalog" component={GiftCatalogScreen} />
                <Stack.Screen name="Cart" component={CartScreen} />
                <Stack.Screen name="Payment" component={PaymentScreen} />
                <Stack.Screen name="Confirmation" component={ConfirmationScreen} />
              </>
            )}
          </Stack.Navigator>
        </NavigationContainer>
      </CartProvider>
    </AuthProvider>
  );
}
