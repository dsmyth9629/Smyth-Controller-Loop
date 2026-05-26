# Smyth-Controller-Loop
Dynamic temperature control loop for AI text generation based on human input entropy and safety triggers.

class SmythController:
    def __init__(self):
        self.creative_temp = 0.7
        self.baseline = 0.7
        self.cooldown = 0
        self.friction = 0.0
        
    def calculate_entropy(self, text):
        """Simple entropy calculator from user text"""
        if not text or len(text) < 3:
            return 0.1
        # Basic character diversity measure
        unique = len(set(text.lower()))
        return unique / len(text)
    
    def update(self, user_text, moral_violation=False):
        entropy = self.calculate_entropy(user_text)
        friction = self.friction
        
        if moral_violation or friction > 1.5:
            self.creative_temp = 0.1
            self.cooldown = 8
            self.friction = 2.0
        elif self.cooldown > 0:
            self.cooldown -= 1
            self.creative_temp = 0.2
        else:
            dT = (0.8 / (entropy + 0.01)) - (1.2 * friction) - (0.3 * (self.creative_temp - self.baseline))
            self.creative_temp += dT * 0.3
            self.creative_temp = max(0.2, min(1.6, self.creative_temp))
        
        self.friction *= 0.85  # friction naturally decays
        return round(self.creative_temp, 2)