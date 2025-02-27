// This is a complete React Native application for Dear Future
// File structure recommendation:
// - App.js (main entry point)
// - Components/ (reusable components)
// - Screens/ (all app screens)
// - Services/ (API and payment integration)
// - Assets/ (images, etc.)

// App.js - Main entry point
import React, { useState, useEffect } from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import AsyncStorage from '@react-native-async-storage/async-storage';

// Import screens
import LoginScreen from './Screens/LoginScreen';
import RegisterScreen from './Screens/RegisterScreen';
import ForgotPasswordScreen from './Screens/ForgotPasswordScreen';
import CalendarScreen from './Screens/CalendarScreen';
import MessageTypeScreen from './Screens/MessageTypeScreen';
import TextMessageScreen from './Screens/TextMessageScreen';
import AudioMessageScreen from './Screens/AudioMessageScreen';
import VideoMessageScreen from './Screens/VideoMessageScreen';
import PhysicalGiftsScreen from './Screens/PhysicalGiftsScreen';
import CartScreen from './Screens/CartScreen';
import PaymentScreen from './Screens/PaymentScreen';
import ProfileScreen from './Screens/ProfileScreen';
import ThankYouScreen from './Screens/ThankYouScreen';

// Theme colors based on logo
const theme = {
  primary: '#FF6B81', // Heart/Love pink
  secondary: '#FFD3D9', // Light pink
  accent: '#FF4757', // Deep pink
  background: '#FFF9FA', // Very light pink
  text: '#333333', // Dark text
  white: '#FFFFFF',
  success: '#2ED573',
  error: '#FF4757',
};

// Create the stack navigator
const Stack = createStackNavigator();

export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [isLoading, setIsLoading] = useState(true);
  
  useEffect(() => {
    // Check if user is logged in
    const checkLoginStatus = async () => {
      try {
        const token = await AsyncStorage.getItem('@auth_token');
        setIsLoggedIn(token !== null);
      } catch (error) {
        console.log('Error checking login status:', error);
      } finally {
        setIsLoading(false);
      }
    };
    
checkLoginStatus();
  }, []);
  
  if (isLoading) {
    return null; // Or a splash screen
  }
  
  return (
    <NavigationContainer>
      <Stack.Navigator 
        initialRouteName={isLoggedIn ? 'Calendar' : 'Login'}
        screenOptions={{
          headerStyle: {
            backgroundColor: theme.primary,
          },
          headerTintColor: theme.white,
          headerTitleStyle: {
            fontWeight: 'bold',
          },
        }}
      >
        <Stack.Screen 
          name="Login" 
          component={LoginScreen} 
          options={{ title: 'Dear Future' }}
        />
        <Stack.Screen 
          name="Register" 
          component={RegisterScreen} 
          options={{ title: 'Create Account' }}
        />
        <Stack.Screen 
          name="ForgotPassword" 
          component={ForgotPasswordScreen} 
          options={{ title: 'Reset Password' }}
        />
        <Stack.Screen 
          name="Calendar" 
          component={CalendarScreen}
          options={{ 
            title: 'Choose Delivery Date',
            headerLeft: null // Prevent going back to login
          }}
        />
        <Stack.Screen 
          name="MessageType" 
          component={MessageTypeScreen}
          options={{ title: 'Select Message Type' }}
        />
        <Stack.Screen 
          name="TextMessage" 
          component={TextMessageScreen}
          options={{ title: 'Write Your Message' }}
        />
        <Stack.Screen 
          name="AudioMessage" 
          component={AudioMessageScreen}
          options={{ title: 'Record Your Message' }}
        />
        <Stack.Screen 
          name="VideoMessage" 
          component={VideoMessageScreen}
          options={{ title: 'Record Your Video' }}
        />
        <Stack.Screen 
          name="PhysicalGifts" 
          component={PhysicalGiftsScreen}
          options={{ title: 'Choose a Gift' }}
        />
        <Stack.Screen 
          name="Cart" 
          component={CartScreen}
          options={{ title: 'Your Cart' }}
        />
        <Stack.Screen 
          name="Payment" 
          component={PaymentScreen}
          options={{ title: 'Payment' }}
        />
        <Stack.Screen 
          name="Profile" 
          component={ProfileScreen}
          options={{ title: 'Your Profile' }}
        />
        <Stack.Screen 
          name="ThankYou" 
          component={ThankYouScreen}
          options={{ title: 'Message Scheduled' }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// LoginScreen.js
import React, { useState } from 'react';
import { 
  View, 
  Text, 
  TextInput, 
  TouchableOpacity, 
  StyleSheet, 
  Image,
  KeyboardAvoidingView,
  Platform,
  ScrollView,
  Alert
} from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function LoginScreen({ navigation }) {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [isLoading, setIsLoading] = useState(false);
  
  const handleLogin = async () => {
    if (!username || !password) {
      Alert.alert('Error', 'Please enter both username and password');
      return;
    }
    
setIsLoading(true);
    
try {
      // Simulate API call - replace with actual authentication
      // In a real app, this would validate credentials with your backend
      await new Promise(resolve => setTimeout(resolve, 1000));
      
// For demo, just store a dummy token
      await AsyncStorage.setItem('@auth_token', 'dummy_token');
      await AsyncStorage.setItem('@user_email', username);
      
// Navigate to calendar screen
      navigation.reset({
        index: 0,
        routes: [{ name: 'Calendar' }],
      });
    } catch (error) {
      Alert.alert('Login Failed', 'Please check your credentials and try again.');
      console.log('Login error:', error);
    } finally {
      setIsLoading(false);
    }
  };
  
  return (
    <KeyboardAvoidingView
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
      style={styles.container}
    >
      <ScrollView contentContainerStyle={styles.scrollContent}>
        <View style={styles.logoContainer}>
          <Image 
            source={require('../Assets/logo.png')} 
            style={styles.logo}
            resizeMode="contain"
          />
          <Text style={styles.appName}>Dear Future</Text>
          <Text style={styles.tagline}>Send love to your future self and loved ones</Text>
        </View>
        
<View style={styles.formContainer}>
          <View style={styles.inputContainer}>
            <FontAwesome name="user" size={20} color="#FF6B81" style={styles.inputIcon} />
            <TextInput
              style={styles.input}
              placeholder="Email or Phone Number"
              value={username}
              onChangeText={setUsername}
              keyboardType="email-address"
              autoCapitalize="none"
            />
          </View>
          
 <View style={styles.inputContainer}>
            <FontAwesome name="lock" size={20} color="#FF6B81" style={styles.inputIcon} />
            <TextInput
              style={styles.input}
              placeholder="Password"
              value={password}
              onChangeText={setPassword}
              secureTextEntry
            />
          </View>
          
<TouchableOpacity 
            style={styles.forgotPassword}
            onPress={() => navigation.navigate('ForgotPassword')}
          >
            <Text style={styles.forgotPasswordText}>Forgot Password?</Text>
          </TouchableOpacity>
          
<TouchableOpacity 
            style={styles.loginButton}
            onPress={handleLogin}
            disabled={isLoading}
          >
            <Text style={styles.loginButtonText}>
              {isLoading ? 'Logging in...' : 'Login'}
            </Text>
          </TouchableOpacity>
          
<View style={styles.registerContainer}>
            <Text style={styles.registerText}>Don't have an account? </Text>
            <TouchableOpacity onPress={() => navigation.navigate('Register')}>
              <Text style={styles.registerLink}>Sign Up</Text>
            </TouchableOpacity>
          </View>
        </View>
        
<View style={styles.heartDecoration}>
          <FontAwesome name="heart" size={30} color="#FF6B81" />
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FFF9FA',
  },
  scrollContent: {
    flexGrow: 1,
    justifyContent: 'center',
    padding: 20,
  },
  logoContainer: {
    alignItems: 'center',
    marginBottom: 40,
  },
  logo: {
    width: 120,
    height: 120,
    marginBottom: 10,
  },
  appName: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#FF6B81',
    marginBottom: 5,
  },
  tagline: {
    fontSize: 16,
    color: '#666',
    textAlign: 'center',
  },
  formContainer: {
    width: '100%',
  },
  inputContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    borderWidth: 1,
    borderColor: '#DDD',
    borderRadius: 10,
    marginBottom: 15,
    backgroundColor: '#FFF',
  },
  inputIcon: {
    padding: 10,
  },
  input: {
    flex: 1,
    paddingVertical: 12,
    paddingRight: 10,
  },
  forgotPassword: {
    alignSelf: 'flex-end',
    marginBottom: 20,
  },
  forgotPasswordText: {
    color: '#FF6B81',
  },
  loginButton: {
    backgroundColor: '#FF6B81',
    paddingVertical: 15,
    borderRadius: 10,
    alignItems: 'center',
    marginBottom: 20,
  },
  loginButtonText: {
    color: '#FFF',
    fontSize: 16,
    fontWeight: 'bold',
  },
  registerContainer: {
    flexDirection: 'row',
    justifyContent: 'center',
  },
  registerText: {
    color: '#666',
  },
  registerLink: {
    color: '#FF6B81',
    fontWeight: 'bold',
  },
  heartDecoration: {
    position: 'absolute',
    bottom: 20,
    right: 20,
  },
});

// CalendarScreen.js
import React, { useState } from 'react';
import { 
  View, 
  Text, 
  StyleSheet, 
  TouchableOpacity,
  Alert,
  ScrollView
} from 'react-native';
import { Calendar } from 'react-native-calendars';
import { FontAwesome } from '@expo/vector-icons';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function CalendarScreen({ navigation }) {
  const [selectedDate, setSelectedDate] = useState('');
  const today = new Date();
  const minDate = today.toISOString().split('T')[0]; // Format: YYYY-MM-DD
  
  const handleDateSelect = (date) => {
    setSelectedDate(date.dateString);
  };
  
  const handleContinue = async () => {
    if (!selectedDate) {
      Alert.alert('Error', 'Please select a future date');
      return;
    }
    
// Save selected date
    try {
      await AsyncStorage.setItem('@selected_date', selectedDate);
      navigation.navigate('MessageType');
    } catch (error) {
      console.log('Error saving date:', error);
      Alert.alert('Error', 'Could not save selected date');
    }
  };
  
  const handleLogout = async () => {
    try {
      await AsyncStorage.removeItem('@auth_token');
      navigation.reset({
        index: 0,
        routes: [{ name: 'Login' }],
      });
    } catch (error) {
      console.log('Error logging out:', error);
    }
  };
  
  return (
    <ScrollView style={styles.container}>
      <View style={styles.calendarContainer}>
        <Text style={styles.heading}>Choose a Future Delivery Date</Text>
        <Text style={styles.subheading}>
          Select when your message should be delivered
        </Text>
        
 <Calendar
          minDate={minDate}
          onDayPress={handleDateSelect}
          markedDates={{
            [selectedDate]: {selected: true, selectedColor: '#FF6B81'}
          }}
          theme={{
            calendarBackground: '#FFF',
            textSectionTitleColor: '#FF6B81',
            selectedDayBackgroundColor: '#FF6B81',
            selectedDayTextColor: '#FFF',
            todayTextColor: '#FF6B81',
            dayTextColor: '#333',
            textDisabledColor: '#d9e1e8',
            dotColor: '#FF6B81',
            selectedDotColor: '#FFF',
            arrowColor: '#FF6B81',
            monthTextColor: '#FF6B81',
            indicatorColor: '#FF6B81',
          }}
          style={styles.calendar}
        />
        
{selectedDate ? (
          <View style={styles.selectedDateContainer}>
            <Text style={styles.selectedDateLabel}>Selected Delivery Date:</Text>
            <Text style={styles.selectedDateValue}>{selectedDate}</Text>
          </View>
        ) : null}
        
 <TouchableOpacity 
          style={[
            styles.continueButton,
            !selectedDate && styles.disabledButton
          ]}
          onPress={handleContinue}
          disabled={!selectedDate}
        >
          <Text style={styles.continueButtonText}>Continue</Text>
          <FontAwesome name="arrow-right" size={16} color="#FFF" />
        </TouchableOpacity>
      </View>
      
 <View style={styles.heartDecoration}>
        <FontAwesome name="heart" size={24} color="#FF6B81" />
      </View>
      
<View style={styles.logoutContainer}>
        <TouchableOpacity style={styles.logoutButton} onPress={handleLogout}>
          <Text style={styles.logoutText}>Logout</Text>
          <FontAwesome name="sign-out" size={16} color="#FF6B81" />
        </TouchableOpacity>
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FFF9FA',
  },
  calendarContainer: {
    padding: 20,
  },
  heading: {
    fontSize: 22,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 8,
    textAlign: 'center',
  },
  subheading: {
    fontSize: 16,
    color: '#666',
    marginBottom: 20,
    textAlign: 'center',
  },
  calendar: {
    borderRadius: 10,
    elevation: 4,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    marginBottom: 20,
  },
  selectedDateContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
    marginBottom: 20,
    paddingVertical: 10,
    backgroundColor: 'rgba(255, 107, 129, 0.1)',
    borderRadius: 8,
  },
  selectedDateLabel: {
    fontSize: 16,
    color: '#333',
    marginRight: 5,
  },
  selectedDateValue: {
    fontSize: A16,
    fontWeight: 'bold',
    color: '#FF6B81',
  },
  continueButton: {
    backgroundColor: '#FF6B81',
    paddingVertical: 15,
    borderRadius: 10,
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
  },
  disabledButton: {
    backgroundColor: '#FFB8C1',
  },
  continueButtonText: {
    color: '#FFF',
    fontSize: 16,
    fontWeight: 'bold',
    marginRight: 10,
  },
  heartDecoration: {
    alignItems: 'center',
    margin: 20,
  },
  logoutContainer: {
    paddingHorizontal: 20,
    paddingBottom: 20,
  },
  logoutButton: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
    paddingVertical: 10,
    borderWidth: 1,
    borderColor: '#FF6B81',
    borderRadius: 8,
  },
  logoutText: {
    color: '#FF6B81',
    marginRight: 10,
  },
});

// MessageTypeScreen.js
import React from 'react';
import { 
  View, 
  Text, 
  StyleSheet, 
  TouchableOpacity,
  Image,
  ScrollView
} from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function MessageTypeScreen({ navigation }) {
  const [messageCount, setMessageCount] = React.useState({
    text: 0,
    audio: 0,
    video: 0
  });
  
  React.useEffect(() => {
    // Load user's message counts
    const loadMessageCounts = async () => {
      try {
        const textCount = await AsyncStorage.getItem('@text_message_count');
        const audioCount = await AsyncStorage.getItem('@audio_message_count');
        const videoCount = await AsyncStorage.getItem('@video_message_count');
        
setMessageCount({
          text: textCount ? parseInt(textCount) : 0,
          audio: audioCount ? parseInt(audioCount) : 0,
          video: videoCount ? parseInt(videoCount) : 0
        });
      } catch (error) {
        console.log('Error loading message counts:', error);
      }
    };
    
loadMessageCounts();
  }, []);
  
  const handleSelectType = async (type) => {
    try {
      await AsyncStorage.setItem('@message_type', type);
      
 switch (type) {
        case 'text':
          navigation.navigate('TextMessage');
          break;
        case 'audio':
          navigation.navigate('AudioMessage');
          break;
        case 'video':
          navigation.navigate('VideoMessage');
          break;
        case 'gift':
          // Check if user is in India before showing physical gifts
          // In a real app, you would use geolocation or ask for country during registration
          const country = await AsyncStorage.getItem('@user_country');
          if (country === 'India') {
            navigation.navigate('PhysicalGifts');
          } else {
            Alert.alert('Not Available', 'Physical gifts are only available in India');
          }
          break;
      }
    } catch (error) {
      console.log('Error saving message type:', error);
    }
  };
  
  const renderFreeMessageInfo = (type) => {
    switch (type) {
      case 'text':
        return messageCount.text < 10 ? 
        `${10 - messageCount.text} free messages remaining` : 
          'Free messages used (₹1 per message)';
      case 'audio':
        return messageCount.audio < 1 ?
          'First audio message is free!' :
          'Free message used (₹5 per message)';
      case 'video':
        return messageCount.video < 1 ?
          'First video message is free!' :
          'Free message used (₹10 per message)';
      default:
        return '';
    }
  };
  
  return (
    <ScrollView style={styles.container}>
      <View style={styles.header}>
        <Text style={styles.heading}>Choose Your Message Type</Text>
        <Text style={styles.subheading}>
          How would you like to share your feelings?
        </Text>
      </View>
      
 <View style={styles.optionsContainer}>
        <TouchableOpacity 
          style={styles.option}
          onPress={() => handleSelectType('text')}
        >
          <View style={styles.optionIconContainer}>
            <FontAwesome name="file-text-o" size={32} color="#FF6B81" />
          </View>
          <View style={styles.optionTextContainer}>
            <Text style={styles.optionTitle}>Text Message</Text>
            <Text style={styles.optionDescription}>
              Write a heartfelt message with emojis and formatting
            </Text>
            <Text style={styles.freeInfo}>
              {renderFreeMessageInfo('text')}
            </Text>
          </View>
          <FontAwesome name="chevron-right" size={16} color="#FF6B81" />
        </TouchableOpacity>
        
 <TouchableOpacity 
          style={styles.option}
          onPress={() => handleSelectType('audio')}
        >
          <View style={styles.optionIconContainer}>
            <FontAwesome name="microphone" size={32} color="#FF6B81" />
          </View>
          <View style={styles.optionTextContainer}>
            <Text style={styles.optionTitle}>Audio Message</Text>
            <Text style={styles.optionDescription}>
              Record your voice for a more personal touch
            </Text>
            <Text style={styles.freeInfo}>
              {renderFreeMessageInfo('audio')}
            </Text>
          </View>
          <FontAwesome name="chevron-right" size={16} color="#FF6B81" />
        </TouchableOpacity>
        
 <TouchableOpacity 
          style={styles.option}
          onPress={() => handleSelectType('video')}
        >
          <View style={styles.optionIconContainer}>
            <FontAwesome name="video-camera" size={32} color="#FF6B81" />
          </View>
          <View style={styles.optionTextContainer}>
            <Text style={styles.optionTitle}>Video Message</Text>
            <Text style={styles.optionDescription}>
              Create a video memory to treasure forever
            </Text>
            <Text style={styles.freeInfo}>
              {renderFreeMessageInfo('video')}
            </Text>
          </View>
          <FontAwesome name="chevron-right" size={16} color="#FF6B81" />
        </TouchableOpacity>
        
 <TouchableOpacity 
          style={styles.option}
          onPress={() => handleSelectType('gift')}
        >
          <View style={styles.optionIconContainer}>
            <FontAwesome name="gift" size={32} color="#FF6B81" />
          </View>
          <View style={styles.optionTextContainer}>
            <Text style={styles.optionTitle}>Physical Gift</Text>
            <Text style={styles.optionDescription}>
              Send a physical gift along with your message
            </Text>
            <Text style={styles.freeInfo}>
              Available only in India
            </Text>
          </View>
          <FontAwesome name="chevron-right" size={16} color="#FF6B81" />
        </TouchableOpacity>
      </View>
      
 <View style={styles.cartContainer}>
        <TouchableOpacity 
          style={styles.cartButton}
          onPress={() => navigation.navigate('Cart')}
        >
          <FontAwesome name="shopping-cart" size={24} color="#FF6B81" />
          <Text style={styles.cartText}>View Cart</Text>
        </TouchableOpacity>
      </View>
      
<View style={styles.pricingInfo}>
        <Text style={styles.pricingTitle}>Pricing Information</Text>
        <Text style={styles.pricingText}>
          • Text Messages: First 10 messages free, then ₹1 per message{'\n'}
          • Audio Messages: First message free, then ₹5 per message{'\n'}
          • Video Messages: First message free, then ₹10 per message{'\n'}
          • Physical Gifts: Price varies by product (India only)
        </Text>
      </View>
      
 <View style={styles.heartDecoration}>
        <FontAwesome name="heart" size={24} color="#FF6B81" />
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FFF9FA',
  },
  header: {
    padding: 20,
    alignItems: 'center',
  },
  heading: {
    fontSize: 22,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 8,
    textAlign: 'center',
  },
  subheading: {
    fontSize: 16,
    color: '#666',
    textAlign: 'center',
  },
  optionsContainer: {
    padding: 15,
  },
  option: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 15,
    backgroundColor: '#FFF',
    borderRadius: 10,
    marginBottom: 15,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 2,
  },
  optionIconContainer: {
    width: 60,
    height: 60,
    borderRadius: 30,
    backgroundColor: '#FFE8EC',
    justifyContent: 'center',
    alignItems: 'center',
    marginRight: 15,
  },
  optionTextContainer: {
    flex: 1,
  },
  optionTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 4,
  },
  optionDescription: {
    fontSize: 14,
    color: '#666',
    marginBottom: 4,
  },
  freeInfo: {
    fontSize: 12,
    color: '#FF6B81',
    fontWeight: 'bold',
  },
  cartContainer: {
    padding: 15,
    alignItems: 'center',
  },
  cartButton: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 12,
    backgroundColor: '#FFE8EC',
    borderRadius: 25,
    paddingHorizontal: 20,
  },
  cartText: {
    marginLeft: 10,
    color: '#FF6B81',
    fontWeight: 'bold',
  },
  pricingInfo: {
    margin: 15,
    padding: 15,
    backgroundColor: '#FFF',
    borderRadius: 10,
    borderLeftWidth: 4,
    borderLeftColor: '#FF6B81',
  },
  pricingTitle: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 8,
  },
  pricingText: {
    fontSize: 14,
    color: '#666',
    lineHeight: 20,
  },
  heartDecoration: {
    alignItems: 'center',
    marginVertical: 20,
  },
});

// TextMessageScreen.js
import React, { useState, useEffect } from 'react';
import { 
  View, 
  Text, 
  StyleSheet, 
  TextInput,
  TouchableOpacity,
  ScrollView,
  Alert,
  KeyboardAvoidingView,
  Platform
} from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import AsyncStorage from '@react-native-async-storage/async-storage';
import EmojiSelector from 'react-native-emoji-selector';

export default function TextMessageScreen({ navigation }) {
  const [messageText, setMessageText] = useState('');
  const [senderName, setSenderName] = useState('');
  const [senderContact, setSenderContact] = useState('');
  const [receiverName, setReceiverName] = useState('');
  const [receiverContact, setReceiverContact] = useState('');
  const [occasion, setOccasion] = useState('');
  const [showEmojiPicker, setShowEmojiPicker] = useState(false);
  const [textStyle, setTextStyle] = useState({
    bold: false,
    italic: false,
    underline: false,
    color: '#000000',
    backgroundColor: '#FFFFFF'
  });
  const [messageCount, setMessageCount] = useState(0);
  const [isFree, setIsFree] = useState(true);
  
  const occasions = [
    'Birthday', 'Anniversary', 'Wedding', 'Graduation', 
    'New Baby', 'New Job', 'Retirement', 'Holiday', 'Other'
  ];
  
  useEffect(() => {
    const loadUserInfo = async () => {
      try {
        const email = await AsyncStorage.getItem('@user_email');
        if (email) {
          setSenderContact(email);
        }
        
const name = await AsyncStorage.getItem('@user_name');
        if (name) {
          setSenderName(name);
        }
        // Load message count to determine if it's free
        const textCount = await AsyncStorage.getItem('@text_message_count');
        const count = textCount ? parseInt(textCount) : 0;
        setMessageCount(count);
        setIsFree(count < 10);
      } catch (error) {
        console.log('Error loading user info:', error);
      }
    };
    
    loadUserInfo();
  }, []);
  
  const handleAddEmoji = (emoji) => {
    setMessageText(messageText + emoji);
  };
  
  const toggleTextStyle = (style) => {
    setTextStyle(prev => ({
      ...prev,
      [style]: !prev[style]
    }));
  };
  
  const handleChangeColor = (color) => {
    setTextStyle(prev => ({
      ...prev,
      color
    }));
  };
  
  const handleChangeBackgroundColor = (color) => {
    setTextStyle(prev => ({
      ...prev,
      backgroundColor: color
    }));
  };
  
  const handleSendMessage = async () => {
    // Validate inputs
